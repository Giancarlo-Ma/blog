---
title: EJ7
date: 2018-12-27 14:07:39
categories:
- Eloquent JavaScript
---

# 项目：机器人

在项目章节，我们将停止学习新知识，我们会一起做个项目。学习编程的理论是必须的，但是阅读和理解实际程序也是很重要的。

我们这个项目会构建一个在虚拟世界实现投递包裹的机器人。

<!-- more -->

## Meadowfield（梅多菲尔德）

MEADOWFIELD村子不是特别大。它包含11个场所和14条街道。可以用这个路数组描述这个村子。
```javascript
const roads = [
  "Alice's House-Bob's House",   "Alice's House-Cabin",
  "Alice's House-Post Office",   "Bob's House-Town Hall",
  "Daria's House-Ernie's House", "Daria's House-Town Hall",
  "Ernie's House-Grete's House", "Grete's House-Farm",
  "Grete's House-Shop",          "Marketplace-Farm",
  "Marketplace-Post Office",     "Marketplace-Shop",
  "Marketplace-Town Hall",       "Shop-Town Hall"
];
```

{% asset_img village2x.png %}

村子中的街道网络形成了一个图。图就是结点和边的集合。这个图将会是机器人活动的世界。

字符串数组不太容易处理。我们感兴趣的是从一个给定位置可以去向哪个终点。让我们转换街道列表到一个数据结构，给定一个位置，告诉我们可达的位置。

```javascript
function buildGraph(edges) {
    let graph = Object.create(null);
    function addEdge(from, to) {
        if(graph[from] == null) {
            graph[from] = [to];
        } else {
            graph[from].push(to);
        }
    }
    //  分离出每条边的起点和终点
    for(let [from, to] of edges.map(edge => edge.split("-"))) {
        addEdge(from, to);
        addEdge(to, from);
    }
    return graph;
}
// 以起点字符串为键，终点数组为值的map数据结构
const roadGraph = buildGraph(roads);
```

给定一个边数组，`buildGraph`创造一个map对象，对于每个结点，存储它相连的结点。

使用`split`方法分离他们为独立的字符串。

## 任务

机器人将会在村子中行走。各个地方都有以某处为终点的包裹。机器人到一个地方拾起包裹，并当他们到达包裹目的地的时候投递他们。

机器人必须在每个点决定下一步去哪。当所有包裹投递完毕机器人完成任务。

为了模拟这个过程，我们必须定义一个描述它的虚拟世界。这个模型告诉我们机器人和包裹现在在哪。当机器人移动到别的地方，我们需要改变状态以反映出新的情况。

如果以面向对象的思维考虑，我们首先需要定义这个世界中的不同元素：一个机器人类，一个包裹类，可能还有一个place类。

这些类保存自己的当前状态，如在某个地方的包裹。当我们改变虚拟世界的时候状态就会改变。

这是错误的。

至少，通常是这样的。一个听上去像对象的东西不意味着应该是程序中的对象。自反性地为应用中的每个概念编写一个类，往往会给你一些相互关联的对象，并且每一个都有自己的内部的变化的状态。这样的程序很难去理解并很容易被破坏。

相反，让我们将村子的状态压缩至一个定义它的值的最小集。有机器人当前的位置，还有尚未投递的包裹，每一个包裹都有当前的为值和目的地地址。就是这样。

当机器人移动，我们不要改变状态而是计算出一个新的移动过后的状态。

```javascript
class VilliageState {
    constructor(place, parcels) {
        this.place = place;
        this.parcels = parcels;
    }
    move(destination) {
        if(!roadGraph[this.place].includes(destination)) {
            return this;
        } else {
            let parcels = this.parcels.map(parcel => {
                if(parcel.place == this.place) {
                    return {place: destination, address: parcel.address};
                } else {
                    return parcel;
                }
            }).filter(parcel => parcel.address != parcel.place);
            return new VilliageState(destination, parcels);
        }
    }
}
```

`move`方法是行为真正发生的地方。首先检查是否终点可达，如果不可达，因为不是一次合法的move，所以直接返回。

然后根据机器人的新位置创造一个新的状态。但是也需要创造一个新的`parcels`。parcels在当前机器人位置的要被带到移动的位置，而我们投递那些parcel的place和address相同的包裹，这两步分别通过map和filter完成。

当parcel对象移动的时候，不会被改变，除了重新创造的时候。move方法给我们一个新的村子状态但是保持旧的状态保持不变。

```javascript
let first = new VillageState(
  "Post Office",
  [{place: "Post Office", address: "Alice's House"}]
);
let next = first.move("Alice's House");

console.log(next.place);
// → Alice's House
console.log(next.parcels);
// → []
console.log(first.place);
// → Post Office
```

move使得parcel被投递，这反映在下一个状态。但是初始状态仍然描述了机器人在邮局，并且包裹尚未投递。

## 持久的数据（persistent data）

不变的数据结构叫做immutable或者是persistent的。它们表现得很像字符串和数字，不会在不同时刻包含不同的东西。

JS中几乎任何东西都能被改变，所以处理持久的值需要一些约束。有一个叫做`Object.freeze`的函数使得写对象属性被忽略。

```javascript
let object = Object.freeze({value: 5});
object.value = 10;
console.log(object.value);
// -> 5
```

为什么我要执意不改变对象呢？显然JS期望我们的对象是可变的。

因为这帮助理解我的程序。再一次关于复杂度管理。当对象在我的系统成为不变的稳定的事物，我就可以独立地考虑对他们的操作，也就是说，从某处移动到Alice的房子总是产生同样的新状态。当对象会随时间变化时，就会为这种类型的推理添加一个新的复杂维度。

对于我们这章构建的小型系统，我们可以处理这样的额外复杂度。但对于我们能构建怎样的系统，最重要的限制就是我们能理解多少。任何使得代码易读的东西都使得构建一个雄心勃勃的系统成为可能。

不幸的是，虽然理解一个构建在持久的数据结构上的系统是很容易的，但是设计一个却是有些难度的，尤其是 当编程语言不提供相关的支持的时候。我们将在这本书中寻找使用持久的数据结构的机会，但是我们也会使用可变的数据结构。

## 模拟

投递机器人观察这个世界的状态决定他要去向何方。因此，我们可以说一个机器人是一个函数，接收`VillageState`对象并返回附近地方的名字。

因为我们想要机器人能记得东西，所以他们可以做出计划和执行计划，我们给他们传递一个memory并返回一个新的memory。因此，机器人返回的应该是一个包含移动的方向和一个memory的对象，这个memory将会在下次调用该函数的时候

```javascript
function runRobot(state, robot, memory) {
    for(let turn = 0; ; turn++) {
        if(state.parcels.length == 0) {
            console.log(`Done in ${turn} turns`);
            break;
        }
        let action = robot(state, memory);
        state = state.move(action.direction);
        memory = action.memory;
        console.log(`Moved to ${action.direction}`);
    }
}
```

考虑下机器人要做什么去解决一个给定的状态。它必须通过访问每个有包裹的地方收取所有的包裹，并且访问每个包裹地址指向的地方（但只能在收取包裹之后）。

最笨的策略就是机器人在每一轮随机选取方向。意味着有极大的困难性，最终会收取到所有的包裹并在某一时间点到达它们应被投递的地方。

```javascript
function randomPick(array) {
    let choice = Math.floor(Math.random() * array.length);
    return array[choice];
}

function randomRobot(state) {
    return {direction: randomPick(roadGraph[state.place])};
}
```
`Math.random()`返回了一个介于0和1之间的数字（总是小于1）。乘以数组长度给定了一个数组的随机索引。

因为机器人不需要记住任何事情，所以他忽略了第二个参数（JS函数对参数数量过多不会有任何怨言）并且忽略了返回对象中的`memory`属性。

为了让这个精细的机器人，我们首先一种用一些包裹创造一个新状态的方式。静态方法(在这里直接在构造器上添加一个属性）是个好地方去实现这种功能。

```javascript
// 用于为我们的函数实现一个随机用例
VilliageState.random = function(parcelCount = 5) {
    let parcels = [];
    for(let i = 0; i < parcelCount; i++) {
        let address = randomPick(Object.keys(roadGraph));
        let place;
        do {
            place = randomPick(Object.keys(roadGraph));
        } while (place == address);
    }
    return new VilliageState("Post Office", parcels);
}
```

我们不向任何包裹的起终点是同一个地方。因为这种原因我们使用`do/while`循环去找到一个和终点不同的起始点。

让我们开启一个虚拟的世界。
```javascript
runRobot(VilliageState.random(), randomRobot);
// → Moved to Marketplace
// → Moved to Town Hall
// → …
// → Done in 63 turns
```

将会花费很多轮去投递包裹，因为没有很好的提前做计划。我们将会很快解决这个问题。

## 邮车的路线

我们应该做的要比随机机器人更好。一个简单的改进就是从真实世界的邮件投递中汲取一些灵感。

如果我们找到一个经过村子中所有地方的路线，机器人可以走两次这条路线，就一定能保证投递完所有的包裹。这里是一个这样的路径（从邮局开始）。
```javascript
const mailRoute = [
  "Alice's House", "Cabin", "Alice's House", "Bob's House",
  "Town Hall", "Daria's House", "Ernie's House",
  "Grete's House", "Shop", "Grete's House", "Farm",
  "Marketplace", "Post Office"
];
```

为了实现route-following机器人，我们需要充分利用机器人的memory。机器人每一轮丢掉第一个元素，并保持memory中剩下的路由。

```javascript
function routeRobot(state, memory) {
    if(memory.length == 0) {
        memory = mailRoute;
    }
    return {direction: memory[0], memory: memory.slice(1)};
}
```

这个机器人运行的快了些。将会花费最多26轮（13步路由的两倍）完成任务但通常会花费更少轮次。

## 寻路

不过，我不会盲目地遵循一个固定的路线调用智能的行为。机器人如果能够根据实际需要做的工作调整它的行为将会变得更加高效。

为了实现这个，它必须有意朝着有包裹的地方移动或是朝着包裹要投递的地方移动。

通过图找路由的问题是典型的搜索问题。我们可以知道是否一个路由是合法的solution，但是不能像计算2+2一样直接计算solution。相反，我们不得不创造可能的solution知道我们找到一个合法的solution。

通过一个图的可能的路由是无穷的。但是当搜索A到B的路由时，我们只对那些起始于A的路由感兴趣。我们也不关心那些访问同一个地方两次的路由，因为无论在哪里肯定都不是最高效的路由。这就减少了寻路者要考虑的路由数量。

事实上，我们最感兴趣的是最短路由。所以我们想要确保在我们查看更长的路由之前先查看短路由。一个好的方法是从起始点增长路由，探索每个未访问的可达的地方，直到一个路由实现了目标。

```javascript
// BFS
function findRoute(graph, from, to) {
  let work = [{at: from, route: []}];
  for (let i = 0; i < work.length; i++) {
    let {at, route} = work[i];
    for (let place of graph[at]) {
      if (place == to) return route.concat(place);
      if (!work.some(w => w.at == place)) {
        work.push({at: place, route: route.concat(place)});
      }
    }
  }
}
```
探索必须以正确的顺序昨晚，到达的第一个地方必须首先被探索。我们不能一到一个地方就立刻探索，因为那意味着从那到达的地方也会立刻被探索。即使可能有其他的未被探索的更短的路径。

因此，函数保持了一个工作列表。这是一个应该下一次被探索的地方的数组，伴随着一个带我们去到那里的路由。开始于起点和一个空路由。

搜索首先获取列表中下一个元素并且探索所有从这个元素出去的，如果其中的某个是目标，路由列表可以被返回。否则，如果还没有访问过这个地方，一个新的元素将被添加到列表中。如果访问过，不需要继续做处理了。

我们的代码没有处理列表中没有更多的元素时的情况，因为我们知道我们的图是连通的，意味着每个地方都可以由其他地方到达。我们总是能找到两点间的路由，搜索永远不会失败。

```javascript
function goalOrientedRobot({place, parcels}, route) {
    
}
```