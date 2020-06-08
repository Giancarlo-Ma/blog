---
title: cs61b-disc06
date: 2018-10-10 13:05:39
categories:
- CS61B
tags:
- java
- data structure
---

# 1 Immutable Rocks
Access control allows us to restrict the use of fields, methods, and classes.
- public: Accessible by everyone.
- protected: Accessible by the class itself, the package, and any subclasses.
- default (no modifier): Accessible by the class itself and the package.
- private: Accessible only by the class itself.
<!-- more -->

A class is immutable if nothing about its instances can change after they are constructed. Which of the following classes are immutable?
```java
public class Pebble {
    public int weight;
    public Pebble() { weight = 1; }
}
```
<span style="color:red">This class is immutable. Pebble's weight field is public, and thus a peddle's state can easily be changed.</span>

```java
public class Rock {
    public final int weight;
    public Rock (int w) { weight = w; }
}
```
<span style="color:red">Immutable because of the weight field is final. Once initialized, can not be changed.</span>

```java
public class Rocks {
    public final Rock[] rocks;
    public Rocks (Rock[] rox) { rocks = rox; }
}
```
<span style="color:red">Though rocks can not be reassigned, we can still change what the array holds and thus Rocks is mutable</span>

```java
public class SecretRocks {
    private Rock[] rocks;
    public SecretRocks(Rock[] rox) { rocks = rox; }
}
```
<span style="color:red">The rocks is private, so outside variable can not reassign it or its element. However, rox can be edited externally after it is passed in, so SecretRocks is technically mutable. This class can be made immutable by using Arrays.copyOf.(`java.util.Arrays.copyOf()` method is in `java.util.Arrays` class. It copies the specified array, truncating or padding with false (if necessary) so the copy has the specified length.)</span>
```java
public class PunkRock {
    private final Rock[] band;
    public PunkRock (Rock yRoad) { band = {yRoad}; }
    public Rock[] myBand() {
        return band;
    }
}
```
<span style="color:red">It is possible to access and modify the contents of PunkRock’s private array through its public myBand() method, so this class is mutable</span>
```java
public class MommaRock {
    public static final Pebble baby = new Pebble();
}
```
<span style="color:red">This class is mutable since Pebble has public variables that can be changed. For instance, given a MommaRock mr, you could mutate mr with mr.baby.weight = 5.</span>

# 2 Breaking the System
2.1 Below is a flawed implementation of the stack ADT.
```java
public class BadIntegerStack {
    public class Node() {
        public Integer value;
        public Node prev;
        public Node(Integer v, Node p) {
            value = v;
            prev = p;
        }
    }
    public Node top;

    public boolean isEmpty() {
        return top == null;
    }

    public void push(Integer num) {
        top = new Node(num, top));
    }

    public Integer pop() {
        Integer ans = top.value;
        top = top.prev;
        return ans;
    }
    public Integer peek() {
        return top.value;
    }
}
```

(a) Exploit the flaw by filling in the main method below so that it prints "Success" by causing BadIntegerStack to produce a NullPointerException.
```java
public static void main(String[] args) {
    try {
        BadIntegerStack stack = new BadIntegerStack();
        stack.pop();
    } catch (NullPointerException e) {
        System.out.println("Success!");
    }
}
```

(b) Exploit another flaw in the stack by completing the main method below so that the stack appears infinitely tall.
```java
public static void main(String[] args) {
    BadIntegerStack stack = new BadIntegerStack();
    stack.push(1);
    stack.top.prev = stack.top;
    while (!stack.isEmpty()) {
        stack.pop();
    }
    System.out.println("This line will never be executed!");
}
```

(c) How can we change the BadIntegerStack class so that it won’t throw NullPointerExceptions or allow ne’er-do-wells to produce endless stacks?

<span style="color:red">Make top private.</span>

# 3 Design a Parking Lot!
3.1 Design a `ParkingLot` package that allocates specific parking spaces to cars in a smart way. Decide what classes you’ll need, and design the API for each. Time permitting, select data structures to implement the API for each class. Try to deal with annoying cases (like disobedient humans).
- Parking spaces can be either regular, compact, or handicapped-only.
- When a new car arrives, the system should assign a specific space based on the type of car.
- All cars are allowed to park in regular spots. Thus, compact cars can park in both compact spaces and regular spaces.
- When a car leaves, the system should record that the space is free.
- Your package should be designed in a manner that allows different parking lots to have different numbers of spaces for each of the 3 types.
- Parking spots should have a sense of closeness to the entrance. When parking a car, place it as close to the entrance as possible. Assume these distances are distinct.

`public class Car`
- `public Car(boolean isCompact, boolean isHandicapped)`: creates a car with given size and permissions.
- `public boolean isCompact()`: returns whether or not a car can fit in a compact space.
- `public boolean isHandicapped()`: returns whether or not a car may park in a handicapped space.
- `public boolean findSpotAndPark(ParkingLot lot)`: attempts to park this car in a spot, returning true if successful.
- `public void leaveSpot()`: vacates this car’s spot.

`private class Spot`
- The Spot class can be declared private and encapsulated by the ParkingLot class. Though it is private, and therefore not a part of our parking lot API, its methods are described here to give you an idea of how a Spot class might be implemented.
- `private Spot(String type, int proximity)`: creates a spot of a given type and proximity.
- `private boolean isHandicapped()`: returns true if this spot is reserved for handicapped drivers.
- `private boolean isCompact()`: returns true if this parking space can only accomodate compact cars.
public class ParkingLot
- `public ParkingLot(int[] handicappedDistances, int[] compactDistances, int[] regularDistances)`: creates a parking lot containing handicappedDistances.length handicapped spaces, each with a distance corresponding to an element of handicappedDistances. Also initializes the appropriate compact and regular spaces.
- `public boolean findSpotAndPark(Car toPark)`: attempts to find a spot and park the given car. Returns false if no spots are available.
- `public void removeCarFromSpot(Car toRemove)`: records when a spot has been vacated, and makes the spot available for parking again. Prioritization of closeness in parking space selection can be handled using several priority queues (one for each kind of parking space). Occupied spaces (which are dequeued when they are assigned) can be tracked with a Map<Car, Spot>
