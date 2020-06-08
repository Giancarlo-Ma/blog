---
title: cs61b-lab09
date: 2018-11-05 12:44:47
categories:
- CS61B
tags:
- java
- data structure
---

# Lab 9: Tree Maps vs. Hash Maps
In this lab, you’ll create BSTMap, a BST-based implementation of the Map61B interface, which represents a basic map. Then, you’ll create MyHashMap, another implementation of the Map61B interface, which instead represents a Hash Map rather than a Tree Map. This lab is pretty long, so it’s unlikely you’ll finish in the allotted time. Don’t worry, though, the autograder is friendly, and you shouldn’t stress about completing every last part, though doing so is great midterm practice.

## 1: BSTMap
The skeleton provides a BSTMap that implements the Map61B interface using a BST (Binary Search Tree) as its core data structure in a file named BSTMap.java. We provide instance variables, a constructor, and a clear method. Your goal is to implement get, put, and size. Other methods such as remove, keySet, and iterator are optional for this lab, and by default should throw an UnsupportedOperationException unless you choose to implement them.

For get and put, you may find it useful to use the putHelper and getHelper helper methods we’ve provided you in the skeleton. We recommend that you work on methods in the order they appear int he file.

Note that the BSTMap class is declared such that the generic keys extend Comparable<K>, so you should use the compareTo method found in the Comparable interface to compare keys.
```java
public static void main(String[] args) {
        BSTMap<String, Integer> bstmap = new BSTMap<>();
        bstmap.put("hello", 5);
        bstmap.put("cat", 10);
        bstmap.put("fish", 22);
        bstmap.put("zebra", 90);
}
```
Solution:
```java
package lab9;

import java.util.Iterator;
import java.util.Set;

/**
 * Implementation of interface Map61B with BST as core data structure.
 *
 * @author Macon
 */
public class BSTMap<K extends Comparable<K>, V> implements Map61B<K, V> {

    private class Node {
        /* (K, V) pair stored in this Node. */
        private K key;
        private V value;

        /* Children of this Node. */
        private Node left;
        private Node right;

        private Node(K k, V v) {
            key = k;
            value = v;
        }
    }

    private Node root;  /* Root node of the tree. */
    private int size; /* The number of key-value pairs in the tree */

    /* Creates an empty BSTMap. */
    public BSTMap() {
        this.clear();
    }

    /* Removes all of the mappings from this map. */
    @Override
    public void clear() {
        root = null;
        size = 0;
    }

    /** Returns the value mapped to by KEY in the subtree rooted in P.
     *  or null if this map contains no mapping for the key.
     */
    private V getHelper(K key, Node p) {
        if (key ==  null) throw new IllegalArgumentException("the argument of get() is null!");
        if (p == null) return null;
        int cmp = key.compareTo(p.key);
        if (cmp < 0) return getHelper(key, p.left);
        if (cmp > 0) return getHelper(key, p.right);
        return p.value;
    }

    /** Returns the value to which the specified key is mapped, or null if this
     *  map contains no mapping for the key.
     */
    @Override
    public V get(K key) {
        return getHelper(key, root);
    }

    /** Returns a BSTMap rooted in p with (KEY, VALUE) added as a key-value mapping.
      * Or if p is null, it returns a one node BSTMap containing (KEY, VALUE).
     */
    private Node putHelper(K key, V value, Node p) {
        if (p == null) {
            size++;
            return new Node(key, value);
        }
        int cmp = key.compareTo(p.key);
        if (cmp < 0) {
            p.left = putHelper(key, value, p.left);
        } else if (cmp > 0) {
            p.right = putHelper(key, value, p.right);
        } else {
            p.value = value;
        }
        return p;
    }

    /** Inserts the key KEY
     *  If it is already present, updates value to be VALUE.
     */
    @Override
    public void put(K key, V value) {
        root = putHelper(key, value, root);
    }

    /* Returns the number of key-value mappings in this map. */
    @Override
    public int size() {
        return size;
    }

    //////////////// EVERYTHING BELOW THIS LINE IS OPTIONAL ////////////////

    /* Returns a Set view of the keys contained in this map. */
    @Override
    public Set<K> keySet() {
        throw new UnsupportedOperationException();
    }

    /** Removes KEY from the tree if present
     *  returns VALUE removed,
     *  null on failed removal.
     */
    @Override
    public V remove(K key) {
        throw new UnsupportedOperationException();
    }

    /** Removes the key-value entry for the specified key only if it is
     *  currently mapped to the specified value.  Returns the VALUE removed,
     *  null on failed removal.
     **/
    @Override
    public V remove(K key, V value) {
        throw new UnsupportedOperationException();
    }

    @Override
    public Iterator<K> iterator() {
        throw new UnsupportedOperationException();
    }
}
```
## 2: MyHashMap
The skeleton provides a MyHashMap that implements the Map61B interface in a file named MyHashMap.java. Your implementation is required to implement get, put, and size. Other methods such as remove, keySet, and iterator are optional for this lab, and by default should throw an UnsupportedOperationException unless you choose to implement them. We’ve provided instance variables for you.

We provide the following constructors:
```java
public MyHashMap();
public MyHashMap(int initialSize);
```
We also provide the clear method and a private hash method that computes the hash function of a key.

Unlike lecture (where each bucket was represented as a naked recursive linked list), each bucket in this lab is implemented as an ArrayMap, similar to what we developed in lecture 13. Because our bucket class is so powerful, your get and put methods will be very short (our get method is only 2 lines, and our put method isn’t many more).

While using such a sophisticated bucket class seems like cheating, it’s not. Delegating work to a helper class is a very important way to manage complexity, and there’s really no reason that the MyHashMap class should be doing things like manually scanning through buckets – that’s the bucket’s job!

Start by implementing get, put, and size with no resizing. After you’ve made figured these out, modify put so that it resizes. You should resize the array of buckets anytime the load factor exceeds MAX_LF, and you should resize multiplicatively (e.g. doubling the number of buckets) rather than arithmetically (e.g. adding 100 new buckets).

You can test your implementation using the TestMyHashMap class in the lab9tester package.

Solution:
```java
package lab9;

import java.util.Iterator;
import java.util.Set;

/**
 *  A hash table-backed Map implementation. Provides amortized constant time
 *  access to elements via get(), remove(), and put() in the best case.
 *
 *  @author Macon
 */
public class MyHashMap<K, V> implements Map61B<K, V> {

    private static final int DEFAULT_SIZE = 16;
    private static final double MAX_LF = 0.75;

    private ArrayMap<K, V>[] buckets;
    private int size;

    private int loadFactor() {
        return size / buckets.length;
    }

    public MyHashMap() {
        buckets = new ArrayMap[DEFAULT_SIZE];
        this.clear();
    }

    public MyHashMap(int capacity) {
        buckets = new ArrayMap[capacity];
        this.clear();
    }
    /* Removes all of the mappings from this map. */
    @Override
    public void clear() {
        this.size = 0;
        for (int i = 0; i < this.buckets.length; i += 1) {
            this.buckets[i] = new ArrayMap<>();
        }
    }

    /** Computes the hash function of the given key. Consists of
     *  computing the hashcode, followed by modding by the number of buckets.
     *  To handle negative numbers properly, uses floorMod instead of %.
     */
    private int hash(K key) {
        if (key == null) {
            return 0;
        }

        int numBuckets = buckets.length;
        return Math.floorMod(key.hashCode(), numBuckets);
    }

    /* Returns the value to which the specified key is mapped, or null if this
     * map contains no mapping for the key.
     */
    @Override
    public V get(K key) {
        if (key == null) throw new IllegalArgumentException("the key is null!");
        int i = hash(key);
        return buckets[i].get(key);
    }

    /* Associates the specified value with the specified key in this map. */
    @Override
    public void put(K key, V value) {
        if (key == null) throw new IllegalArgumentException("first argument to put is null");
        if (loadFactor() > MAX_LF) {
            resize(2 * buckets.length);
        }
        int i = hash(key);
        if (!buckets[i].containsKey(key)) size++;
        buckets[i].put(key, value);
    }

    private void resize(int capacity) {
        MyHashMap<K, V> mhm = new MyHashMap<>(capacity);
        for (int i = 0; i < buckets.length; i++) {
            for (K key : buckets[i].keySet()) {
                mhm.put(key, buckets[i].get(key));
            }
        }
        this.size = mhm.size;
        this.buckets = mhm.buckets;
    }
    /* Returns the number of key-value mappings in this map. */
    @Override
    public int size() {
        return size;
    }

    //////////////// EVERYTHING BELOW THIS LINE IS OPTIONAL ////////////////

    /* Returns a Set view of the keys contained in this map. */
    @Override
    public Set<K> keySet() {
        throw new UnsupportedOperationException();
    }

    /* Removes the mapping for the specified key from this map if exists.
     * Not required for this lab. If you don't implement this, throw an
     * UnsupportedOperationException. */
    @Override
    public V remove(K key) {
        throw new UnsupportedOperationException();
    }

    /* Removes the entry for the specified key only if it is currently mapped to
     * the specified value. Not required for this lab. If you don't implement this,
     * throw an UnsupportedOperationException.*/
    @Override
    public V remove(K key, V value) {
        throw new UnsupportedOperationException();
    }

    @Override
    public Iterator<K> iterator() {
        throw new UnsupportedOperationException();
    }
}

```
