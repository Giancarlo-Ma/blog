---
title: distance-vector
date: 2018-12-07 12:41:08
categories:
- 计算机网络
tags:
- 路由算法
mathjax: true
---

# 距离矢量算法

- 迭代式：算法持续进行直至无信息需要在邻居间交换
- 分布式：每个节点收到邻居传送过来的路由信息，计算后再分发计算结果给邻居
- 异步：不需要所有的节点同时操作
- 自终止：算法不需要终止信号，知道什么时候应该终止

<!-- more -->

## 算法过程

我们表示从a到b的代价为$c_ab$，然后通过相邻节点之间的路由信息交换，根据Bellman-Ford方程，我们有
$$d_xy=minv(c_xv+d_vy)$$
显而易见，对于与x相邻的任何节点v，若$d_vy$是v到y的最短代价，那么x到y的最短代价就是与x相邻的所有节点里面$c_xv+d_vy$最小的那个，这也是这个方程影射出的含义。

对于x节点来说，需要维护如下信息：
- x节点到每个相邻节点v的代价，记为$c_xv$
- x节点自身的距离向量，可以是一个到图中所有节点的估计代价数组
- x每个邻居节点v的距离向量。

只要根据Bellman-Ford方程不断计算自身的距离向量，并将产生更新后的结果不断分发到相邻节点，经过若干次迭代之后，x到图中所有节点的估计代价就会收敛于实际的代价。

## 算法伪代码
```
Initialization:
    for all destinations y in N:
        D(x, y) = c(x,y) if y is not a neighbor then c(x,y) = ∞
    for each neighbor w:
        D(w, y) = ? for all destinations y in N
    for each neighbor w:
        send distance vector D(x, y) = [D(x, y) : y in N] to w
loop:
    wait (until I see a link cost change to some neighbor w or until I receive a distance vector from some neighbor w)

    for each y in N:
        D(x, y) = minv(c(x, v) + D(v, y))

    if D(x, y) changed for any destination y
        send sitance vector D(x, y) = [D(x, y) : y in N] to all neighbors
forever
```

## 项目实现

### 计算转发表
```python
def update_forwarding_table(self):
    for port, peer_table in self.peer_tables.items():
        for host, entry in peer_table.items():
            if not host in self.forwarding_table:
                self.forwarding_table[host] = ForwardingTableEntry(host, port, self.link_latency[port] + entry.latency)
            else:
                if self.forwarding_table[host].latency > self.link_latency[port] + entry.latency:
                    self.forwarding_table[host] = ForwardingTableEntry(host, port, self.link_latency[port] + entry.latency)
```
遍历所有相邻节点，如果某个终点主机不存在我们的转发表中，简单添加一个条目，否则，需要将旧的转发代价与新的转发代价作比较，如果新的小，就更新我们的转发表中的条目。

### 转发数据包
```python
def handle_data_packet(self, packet, in_port):
        """
        Called when a data packet arrives at this router.

        You may want to forward the packet, drop the packet, etc. here.

        :param packet: the packet that arrived.
        :param in_port: the port from which the packet arrived.
        :return: nothing.
        """
        # TODO: fill this in!
        if packet.dst in self.forwarding_table and self.forwarding_table[packet.dst].latency < INFINITY and self.forwarding_table[packet.dst].port != in_port:
            self.send(packet, self.forwarding_table[packet.dst].port)
```
首先需要确定packet的dst在我们的转发表中才有可能转发，然后转发表中到dst的latency不能大于INFINITY，而且转发表的出端口不能与入端口相同。只有同时满足这些条件，我们才调用send方法向合适的端口转发该包。

### 通知路由（advertise route）

```python
def handle_link_up(self, port, latency):
        """
        Called by the framework when a link attached to this router goes up.

        :param port: the port that the link is attached to.
        :param latency: the link latency.
        :returns: nothing.
        """
        self.link_latency[port] = latency
        self.peer_tables[port] = PeerTable()

        # TODO: fill in the rest!
        for host, entry in self.forwarding_table.items():
            packet = basics.RoutePacket(host, entry.latency)
            self.send(packet, port)
```

转发表中的每一个路由信息都发送给新加入的链路的另一端。

```python
def send_routes(self, force=False):
        """
        Send route advertisements for all routes in the forwarding table.

        :param force: if True, advertises ALL routes in the forwarding table;
                      otherwise, advertises only those routes that have
                      changed since the last advertisement.
        :return: nothing.
        """
        # TODO: fill this in!
        if force == True:
            for port in self.peer_tables:
                for host, entry in self.forwarding_table.items():
                    if entry.port != port:
                        if entry.latency >= INFINITY:
                            route_packet = basics.RoutePacket(host, INFINITY)
                        else:
                            route_packet = basics.RoutePacket(host, entry.latency)
                        self.send(route_packet, port)
```
split horizon检测，如果一个转发表的entry的出端口刚好和要advertise的端口相同，我们就跳过这一个，因为这条路由信息来自该端口，就不要把这条信息回发给该端口。另外就是当转发的latency大于等于INFINITY时，我们发送的路由信息的latency要标记为INFINITY

### 添加静态路由

与路由器直接相连的主机，需要添加一条永不过期的静态路由。做法是通过更新路由器的peer_tables里面该端口的peer_table，添加一个entry，表明这个主机到自己本身的latency为0，并且过期时间为FOREVER，然后我们更新转发表，并在更新转发表之后将全部路由信息重新发送至各个相连端口（通过send_routes方法)。
```python
def add_static_route(self, host, port):
        """
        Adds a static route to a host directly connected to this router.

        Called automatically by the framework whenever a host is connected
        to this router.

        :param host: the host.
        :param port: the port that the host is attached to.
        :returns: nothing.
        """
        # `port` should have been added to `peer_tables` by `handle_link_up`
        # when the link came up.
        assert port in self.peer_tables, "Link is not up?"

        # TODO: fill this in!
        self.peer_tables[port][host] = PeerTableEntry(host, 0, PeerTableEntry.FOREVER)
        self.update_forwarding_table()
        self.send_routes()
```
### 处理路由通知(handle route advertisement)

```python
def handle_route_advertisement(self, dst, port, route_latency):
        """
        Called when the router receives a route advertisement from a neighbor.

        :param dst: the destination of the advertised route.
        :param port: the port that the advertisement came from.
        :param route_latency: latency from the neighbor to the destination.
        :return: nothing.
        """
        # TODO: fill this in!
        self.peer_tables[port][dst] = PeerTableEntry(dst, route_latency, api.current_time()+ROUTE_TTL)
        self.update_forwarding_table()
        self.send_routes()
```
首先更新peer_tables里面的相关信息，然后因为peer_tables的改变，我们通过update_forwarding_table方法重新计算转发表，并分发新的路由信息给各个端口。

### 移除路由
```python
def handle_link_down(self, port):
        """
        Called by the framework when a link attached to this router does down.

        :param port: the port number used by the link.
        :returns: nothing.
        """
        # TODO: fill this in!
        for host, entry in self.forwarding_table.items():
            if entry.port == port:
                del self.forwarding_table[host]
        del self.peer_tables[port]
        del self.link_latency[port]
        self.update_forwarding_table()
        self.send_routes()
```
当与路由器连接的某段链路被移除后，首先要移除转发表中所有从该端口转发的条目；然后要移除peer_tables从该端口获取的路由信息，链路延迟也要删除这个端口的条目，由于改变了peer_tables，所以我们重新计算转发表并将改变后的路由信息分发给各个端口。
```python
def expire_routes(self):
        """
        Clears out expired routes from peer tables; updates forwarding table
        accordingly.
        """
        # TODO: fill this in!
        for port, table in self.peer_tables.items():
            for dst, entry in table.items():
                if api.current_time() > entry.expire_time:
                    table.pop(dst)
        self.update_forwarding_table()
```
对于当前时间已经大于过期时间，就删除相应条目，处理完peer_tables过后，要对转发表重新计算。
