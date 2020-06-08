---
title: cs61b-examprep05
date: 2018-10-06 13:51:54
categories:
- CS61B
tags:
- java
- data structure
---

# Assorted ADTs
A **list** is an ordered collection, or *sequence*.
```java
interface List<E> {
  boolean add(E element); // add element to the end of the list
  void add(int index, E element); // adds element at the given index
  E get(int index); // returns element at the given index
  int size(); // the number of elements in the list
}
```
<!-- more -->
A **set** is a (usually unordered) collection of unique elements.
```java
interface Set<E> {
  void add(E element); // adds element to the collection
  boolean contains(Object object); // checks if set containes object
  int size(); // number of elements in the set
  boolean remove(Object object); // removes specified object from set
}
```
A **map** is a collection of key-value mappings, like a dictionary in Python. Like a set, the keys in a map is unique.
```java
interface Map<K,V> {
  V put(K key, V value); // adds key-value pair to the map
  V get(K key); // returns value for the corresponding key
  boolean containsKey(K key); //checks if the map contains the specified key
  Set<K> keySet(); // returns set of all keys in map
}
```
Stacks and queues are two similar types of linear collections with special behavior.A **stack** is a last-in, first-out ADT: elements are always added or removed from one end of the data structure. A **queue** is a first-in, first-out ADT. Both data types support three basic operations: `push(e)` which adds an element, `peek()` which returns the next element, and `poll()` which returns and removes the next element.
Java defines an interface that combines both stacks and queues in the Deque. A **deque** (double ended queue, pronounced “deck”) is a linear collection that supports element insertion and removal at both ends.
```java
interface Deque<E> {
  void addFirst(E e); // adds e to the front of deque
  E removeFirst(); // removes and returns front element of deque
  E getFirst(); // returns front element of deque
  void addLast(e); // adds e to end of deque
  E removeLast(); // removes and returns last element of deque
  E getLast(); // returns last element of deque
}
```
Generally-speaking, a **priority queue** is like a regular queue except each element has a priority associated with it which determines in what order elements are removed from the queue. In Java, `PriorityQueue` is a class, a heap data structure implementing the priority queue ADT. The priority is determined by either natural
order (`E implements Comparable<E>`) or a supplied `Comparator<E>`.
```java
class PriorityQueue<E> {
  boolean add(E e); // adds element e to the priority queue
  E peek(); // looks at the highest priority element, but does not remove it from the PQ
  E poll(); // pops the highest priority element from the PQ
}
```

# Use them

a. Given an array of integers A and an integer k, return true if any two numbers in the array sum up to k, and return false otherwise. How would you do this? Give the main idea and what ADT you would use.

<span style="color:red">The fastest way to do this is with the help of a set (specifically, a HashSet, which has constant time add() and contains()). The key insight is that is that if a + b = x, then b = x - a. This means that we can look to see whether or not x - (current element) has been seen already. We can store every element that we look through in a set, and do a single pass through the array.</span>

```java
public boolean twoSum(int[] A, int k) {
  Set<Integer> prevSeen = new Set<>();
  for (int i : A) {
    if (prevSeen.contains(k - i)) {
      return true;
    }
    prevSeen.add(i);
  }
  return false;
}
```

b. Find the k most common words in a document. Assume that you can represent this as an array of Strings, where each word is an element in the array. You might find using multiple data structures useful.

<span style="color:red">Keep a count of all the words in the document using a HashMap<String, Integer>. After we go through all of the words, each word will be mapped to how many times it's appeared. What we can do is to put all the words into a MaxPriorityQueue<String>, using a custom comparator that compares words based on the counts in the HashMap. We can then pop off the k most common words by just calling poll() on the MaxPriorityQueue k times.</span>

```java
public static void topKPopularWords (String[] words, int k) {
  Map<String, Integer> counts = new HashMap<>();
  for (String word : words) {
    if (!counts.containsKey(word)) {
      counts.put(word, 1);
    } else {
      counts.put(word, counts.get(word) + 1)
    }
  }
  PriorityQueue<String> pq = new PriorityQueue<>(new Comparator<String>(){
    @Override
    public int compare (String a, String b) {
      return counts.get(b) - counts.get(a);
    }
  });
  for (String word : counts.keySet()) {
    pq.add(word);
  }
  for (int i = 0; i < k; i++) {
    System.out.println(pq.poll());
  }
}
```

# Mutant ADTs

a. Define a Queue class that implements the push and poll methods of a queue ADT using only a Stack class which implements the stack ADT.
*Hint: Try using two stacks*
```java
public class Queue<E> {
  private Stack<E> stack = new Stack<>();

  public void push (E e) {
    stack.push(e);
  }

  public E poll () {
    return poll(stack.pop());
  }

  public E poll (E previous) {
    if (stack.isEmpty()) {
      return previous;
    }
    E current = stack.poll();
    E toReturn = poll(current);
    stack.push(current);
    return toReturn;
  }
}
```

b. Suppose we wanted a data structure `SortedStack` that takes in integers, and maintains them in sorted order. `SortedStack` supports two operations: `push(int i)` and `pop()`. Pushing puts an int onto our `SortedStack`, and popping returns the next smallest item in the `SortedStack`. For example, if we inserted 10, 4, 8, 2, 14, and 3 into a `SortedStack`, and then popped everything off, we would get 2, 3, 4, 8, 10, 14.

<span style="color:red">The solution to this is very similar to that of the question above. Once again,we will have two stacks, A and B. A will hold all the items, and B will be our buffer again. This time, when we add something to the queue, we continue to pop items off from A and push it onto B until the next item that will be popped(we can access this via peek()) is greater than or equal to the item we’re adding to it. At that point, we can push our item onto A, and then pop everything from B and push them back into A. Thus, we maintain the sorted-ness of our SortedStack.</span>
```java
public class SortedStack<Item implements Comparable<Item>> {
  private Stack<Item> a;
  private Stack<Item> b;

  public SortedStack() {
    a = new Stack<>();
    b = new Stack<>();
  }

  public void push(Item i) {
    while (!a.isEmpty() && a.peek() < i) {
      b.push(a.pop());
    }
    a.push(i);
    while (!b.isEmpty()) {
      a.push(b.pop());
    }
  }

  public Item poll() {
    return a.pop();
  }
}
```
