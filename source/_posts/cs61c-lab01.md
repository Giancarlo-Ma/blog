---
title: cs61c-lab01
date: 2018-09-25 09:39:44
categories:
- CS61C
tags:
- Machine Structure
- C
---

# Compiling and Running a C Program
In this lab, we will be using the command line program gcc to compile programs in C. The simplest way to run gcc is as follows (note that there is no file called program.c given in the lab files, this is just an example for your information).
```
$ gcc program.c
```
This compiles program.c into an executable file named a.out. This file can be run with the following command.
```
$ ./a.out
```

<!-- more -->
gcc has various command line options which you are encouraged to explore. In this lab, however, we will only be using -o, which is used to specify the name of the executable file that gcc creates. Using -o, you would use the following commands to compile program.c into a program named program, and then run it.
```
$ gcc -o program program.c
$ ./program
```
# Exercise 1: Simple C Program
In this exercise, we will see an example of preprocessor macro definitions. Macros can be a messy topic, but in general the way they work is that before a C file is compiled, all macro constant names are replaced exactly with the value they refer to.

In the scope of this exercise, we will be using macro definitions exclusively as global constants. Here we define CONSTANT_NAME to refer to literal_value (an integer literal). Note that there is only a space separating name from value.

```c
#define CONSTANT_NAME literal_value
```
Now, look at the code contained in [eccentric.c](http://inst.eecs.berkeley.edu/~cs61c/sp15/labs/01/eccentric.c). We see four different examples of basic C control flow. First compile and run the program to see what it does. Play around with the constant values of the four macro: V0 through V3. See how changing them changes the program output. Modifying only these four values, make the program produce the following output.
```
$ gcc -o eccentric eccentric.c
$ ./eccentric
Berkeley eccentrics:
====================
Happy Happy Happy
Yoshua

Go BEARS!
```
There are actually several different combinations of values that can give this output. You should consider what is the minimum number of different values V0 through V3 can have to give this same output. The maximum is four, when they are all distinct from each other.


# Debugger
[GDB reference card](http://inst.eecs.berkeley.edu/~cs61c/resources/gdb5-refcard.pdf)

- How do you set a breakpoint which only occurs when a set of conditions is true (e.g. when certain variables are a certain value)?

- How do you execute the next line of C code in the program after stopping at a breakpoint?

- If the next line of code is a function call, you'll execute the whole function call at once if you use your answer to #3. How do you tell GDB that you want to debug the code inside the function instead?

- How do you resume the program after stopping at a breakpoint?

- How can you see the value of a variable (or even an expression like 1+2) in gdb?

- How do you configure gdb so it prints the value of a variable after every step?

- How do you print a list of all variables and their values in the current function?

- How do you exit out of gdb?

# Exercise 3: Debugging a buggy C program
You will now use your newly acquired gdb knowledge to debug a short C program! Consider the program [ll_equal.c](http://inst.eecs.berkeley.edu/~cs61c/sp15/labs/01/ll_equal.c). Compile and run the program, and experiment with it. It will give you the following result:
```
$ gcc -g -o ll_equal ll_equal.c
$ ./ll_equal
equal test 1 result = 1
Segmentation fault
```
Now, start gdb on the program, following the instructions in exercise 1. Set a breakpoint in the ll_equal() function, and run the program. When the debugger returns at the breakpoint, step through the instructions in the function line by line, and examine the values of the variables. Pay attention to the pointers a and b in the function. Are they always pointed to the right address? Find the bug and fix it.

**By debugging we find that during the second function call, there seems to be have a situation that we want b's value when b is null!This is null pointer reference, which seems to be totally wrong.So we need to determin whether a or b is null, then compare value with each other.**
```c
#include <stdio.h>

typedef struct node {
	int val;
	struct node* next;
} node;

/* FIXME: this function is buggy. */
int ll_equal(const node* a, const node* b) {
	while (a != NULL) {
		if (b == NULL || a->val != b->val)
			return 0;
		a = a->next;
		b = b->next;
	}
	/* lists are equal if a and b are both null */
	return a == b;
}

int main(int argc, char** argv) {
	int i;
	node nodes[10];

	for (i=0; i<10; i++) {
		nodes[i].val = 0;
		nodes[i].next = NULL;
	}

	nodes[0].next = &nodes[1];
	nodes[1].next = &nodes[2];
	nodes[2].next = &nodes[3];

	printf("equal test 1 result = %d\n", ll_equal(&nodes[0], &nodes[0]));
	printf("equal test 2 result = %d\n", ll_equal(&nodes[0], &nodes[2]));

	return 0;
}
```

# Exercise 4: Pointers and Structures in C
Here's one to help you in your interviews. In [ll_cycle.c](http://inst.eecs.berkeley.edu/~cs61c/sp15/labs/01/ll_cycle.c), complete the function ll_has_cycle() to implement the following algorithm for checking if a singly-linked list has a cycle.

Start with two pointers at the head of the list. We'll call the first one tortoise and the second one hare.
Advance hare by two nodes. If this is not possible because of a null pointer, we have found the end of the list, and therefore the list is acyclic.
Advance tortoise by one node. (A null pointer check is unnecessary. Why?)
If tortoise and hare point to the same node, the list is cyclic. Otherwise, go back to step 2.
After you have correctly implemented ll_has_cycle(), the program you get when you compile ll_cycle.c will tell you that ll_has_cycle() agrees with what the program expected it to output.
```c
#include <stdio.h>

typedef struct node {
	int value;
	struct node *next;
} node;

int ll_has_cycle(node *head) {
	/* your code here */
	if (head == NULL) {
		return 0;
	}
	node *tortoise = head;
	node *hare = head;
	while (hare -> next != NULL && hare -> next -> next != NULL) {
		tortoise = tortoise -> next;
		hare = hare -> next -> next;
		if (tortoise == hare) {
			return 1;
		}
	}
	return 0;
}

void test_ll_has_cycle(void) {
	int i;
	node nodes[25]; //enough to run our tests
	for(i=0; i < sizeof(nodes)/sizeof(node); i++) {
		nodes[i].next = 0;
		nodes[i].value = 0;
	}
	nodes[0].next = &nodes[1];
	nodes[1].next = &nodes[2];
	nodes[2].next = &nodes[3];
	printf("Checking first list for cycles. There should be none, ll_has_cycle says it has %s cycle\n", ll_has_cycle(&nodes[0])?"a":"no");

	nodes[4].next = &nodes[5];
	nodes[5].next = &nodes[6];
	nodes[6].next = &nodes[7];
	nodes[7].next = &nodes[8];
	nodes[8].next = &nodes[9];
	nodes[9].next = &nodes[10];
	nodes[10].next = &nodes[4];
	printf("Checking second list for cycles. There should be a cycle, ll_has_cycle says it has %s cycle\n", ll_has_cycle(&nodes[4])?"a":"no");

	nodes[11].next = &nodes[12];
	nodes[12].next = &nodes[13];
	nodes[13].next = &nodes[14];
	nodes[14].next = &nodes[15];
	nodes[15].next = &nodes[16];
	nodes[16].next = &nodes[17];
	nodes[17].next = &nodes[14];
	printf("Checking third list for cycles. There should be a cycle, ll_has_cycle says it has %s cycle\n", ll_has_cycle(&nodes[11])?"a":"no");

	nodes[18].next = &nodes[18];
	printf("Checking fourth list for cycles. There should be a cycle, ll_has_cycle says it has %s cycle\n", ll_has_cycle(&nodes[18])?"a":"no");

	nodes[19].next = &nodes[20];
	nodes[20].next = &nodes[21];
	nodes[21].next = &nodes[22];
	nodes[22].next = &nodes[23];
	printf("Checking fifth list for cycles. There should be none, ll_has_cycle says it has %s cycle\n", ll_has_cycle(&nodes[19])?"a":"no");

	printf("Checking length-zero list for cycles. There should be none, ll_has_cycle says it has %s cycle\n", ll_has_cycle(NULL)?"a":"no");
}

int main(void) {
  test_ll_has_cycle();
  return 0;
}

```
