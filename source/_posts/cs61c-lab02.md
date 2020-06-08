---
title: cs61c-lab02
date: 2018-10-12 17:10:06
categories:
- CS61C
tags:
- C
- Machine Structure
---

# Exercise 1: Bit Operations
For this exercise, you will complete `bit_ops.c` by implementing the following three bit manipulation functions. You will want to use bitwise operations such as and (&), or (|), xor (^), not (~), left shifts (<<), and right shifts (>>). Avoid using any loops or conditional statements.
<!-- more -->
```c
// Return the nth bit of x.
// Assume 0 <= n <= 31
unsigned get_bit(unsigned x,
                 unsigned n);

// Set the nth bit of the value of x to v.
// Assume 0 <= n <= 31, and v is 0 or 1
void set_bit(unsigned * x,
             unsigned n,
             unsigned v);

// Flip the nth bit of the value of x.
// Assume 0 <= n <= 31
void flip_bit(unsigned * x,
              unsigned n);
```
Once you complete these functions, you can compile and run your code using the following commands.
```
$ make bit_ops
$ ./bit_ops
```
This will print out the result of a few limited tests.

Checkoff [1/3]
- Show how you implemented get, set, and flip.
- Show the output of running the tests.

```c
// Return the nth bit of x.
// Assume 0 <= n <= 31
unsigned get_bit(unsigned x,
                 unsigned n) {
    // YOUR CODE HERE
    // Returning -1 is a placeholder (it makes
    // no sense, because get_bit only returns
    // 0 or 1)
    return (x >> n) & 1;
}
// Set the nth bit of the value of x to v.
// Assume 0 <= n <= 31, and v is 0 or 1
void set_bit(unsigned * x,
             unsigned n,
             unsigned v) {
    // YOUR CODE HERE
    int mask = 1 << n;
    (v == 1) ? (*x) |= mask : ((*x) &= (~mask));
    // Or (* x) = ((* x) & (0xFFFFFFFF - (1 << n))) + (v << n);
    // Make that bit to be manipulated to zero, then plus the v bit.
}
// Flip the nth bit of the value of x.
// Assume 0 <= n <= 31
void flip_bit(unsigned * x,
              unsigned n) {
    // YOUR CODE HERE
    (*x) ^= (1 << n);
}
```
# Exercise 2: Linear feedback shift register
In this exercise, you will implement a lfsr_calculate() function to compute the next iteration of a linear feedback shift register (LFSR). Applications that use LFSRs are: Digital TV, CDMA cellphones, Ethernet, USB 3.0, and more! This function will generate pseudo-random numbers using bitwise operators. For some more background, read the Wikipedia article on Linear feedback shift registers. In lfsr.c, fill in the function lfsr_calculate() so that it does the following:
## Hardware diagram(see explanation below)

{% asset_img LFSR-F16.gif %}

## Explanation of the above diagram
- On each call to lfsr_calculate, you will shift the contents of the register 1 bit to the right.
- This shift is neither a logical shift or an arithmetic shift. On the left side, you will shift in a single bit equal to the Exclusive Or (XOR) of the bits originally in position 11, 13, 14, and 16.
- The curved head-light shaped object is an XOR, which takes two inputs (a, b) and outputs a^b.
- If you implemented lfsr_calculate() correctly, it should output all 65535 positive 16-bit integers before cycling back to the starting number.

After you have correctly implemented lfsr_calculate(), compile lfsr and run it. Your output should be similar to the following:
```
$ make lfsr
$ ./lfsr
My number is: 1
My number is: 5185
My number is: 38801
My number is: 52819
My number is: 21116
My number is: 54726
My number is: 26552
My number is: 46916
My number is: 41728
My number is: 26004
My number is: 62850
My number is: 40625
My number is: 647
My number is: 12837
My number is: 7043
My number is: 26003
My number is: 35845
My number is: 61398
My number is: 42863
My number is: 57133
My number is: 59156
My number is: 13312
My number is: 16285
 ... etc etc ...
Got 65535 numbers before cycling!
Congratulations! It works!
```

Checkoff [2/3]
- Show how you implemented your lfsr_calculate function.
- Show the output from running ./lfsr.

```c
void lfsr_calculate(uint16_t *reg) {

  /* YOUR CODE HERE */
  uint16_t bit = ((*reg) >> 0) ^ ((*reg) >> 2) ^ ((*reg) >> 3) ^ ((*reg) >> 5);
  *reg = (*reg >> 1) | (bit << 15);
}
```
I wrote the answer according to [wiki]( https://en.wikipedia.org/wiki/Linear-feedback_shift_register)

# Exercise 3: Memory Management
This exercise uses vector.h, vector-test.c, and vector.c, where we provide you with a framework for implementing a variable-length array. This exercise is designed to help familiarize you with C structs and memory management in C.

**Your task is to explain why the two functions bad_vector_new() and also_bad_vector_new() are bad and fill in the functions vector_new(), vector_get(), vector_delete(), and vector_set() in vector.c so that our test code vector-test.c runs without any memory management errors.** Comments in the code describe how the functions should work. Look at the functions we've filled in to see how the data structures should be used. For consistency, it is assumed that all entries in the vector are 0 unless set by the user. Keep this in mind as malloc() does not zero out the memory it allocates.

For explaining why the two bad functions are incorrect, keep in mind that one of these functions will actually run correctly (assuming correctly modified vector_new, vector_set, etc.) but there may be other problems; hint: think about memory usage.
## Using Valgrind
To help you to find memory bugs, we have installed a copy of Valgrind Memcheck. Valgrind is ONLY on the lab machines in the Hive and the Orchard. This program will run an executable while keeping track of what registers and regions of memory contain allocated and/or initialized values. The program will run much slower (by a factor of about 10 to 50) so that this information can be collected, but Valgrind Memcheck can identify many memory errors automatically at the point at which they are produced. You will need to learn the basics of how to parse the Valgrind output.

You can test your code in the following two ways:
```bash
// 1) to check functionality:
$ make vector-test
$ ./vector-test

// 2) to check memory management using Valgrind:
$ make vector-memcheck
```
The Makefile calls Valgrind as follows:
```
$ valgrind --tool=memcheck --leak-check=full --track-origins=yes [OS SPECIFIC ARGS] ./<executable>
```
The --track-origins flag attempts to identify the sources of unitialized values. The --leak-check=full option tries to identify memory leaks. [OS SPECIFIC ARGS] are simply a set of arguments to Valgrind that differ across operating systems (in our case, Ubuntu (Linux)). If you are interested in learning more about these, see the Makefile.

The last line in the Valgrind output is the line that will indicate at a glance if things have gone wrong. Here's a sample output from a buggy program:
```
==47132== ERROR SUMMARY: 1200039 errors from 24 contexts (suppressed: 18 from 18)
```
If your program has errors, you can scroll up in the command line output to view details for each one. For our purposes, you can safely ignore all output that refers to suppressed errors. In a leak-free program, your output will look like this:
```
==44144== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 18 from 18)
```
Again, any number of suppressed errors is fine; they do not affect us.

Feel free to also use a debugger or add printf statements to vector.c and vector-test.c to debug your code.

Checkoff [3/3]
Show your modifications to vector.c.
Show the output from running make vector-memcheck.


```c
/* Bad example of how to create a new vector */
/* v is allocated in stack so when the function exits stack, the retval pointer may poinnt to garbage*/
vector_t *bad_vector_new() {
	/* Create the vector and a pointer to it */
	vector_t *retval, v;
	retval = &v;

	/* Initialize attributes */
	retval->size = 1;
	retval->data = malloc(sizeof(int));
	if (retval->data == NULL) {
		allocation_failed();
	}

	retval->data[0] = 0;
	return retval;
}
```

```c
/* Another suboptimal way of creating a vector */
/* This is bad also because the v variable is allocated in stack. When function is returned ,the stack memory will be recycled.(Maybe I think) */
vector_t also_bad_vector_new() {
	/* Create the vector */
	vector_t v;

	/* Initialize attributes */
	v.size = 1;
	v.data = malloc(sizeof(int));
	if (v.data == NULL) {
		allocation_failed();
	}
	v.data[0] = 0;
	return v;
}
```
```c
/* Create a new vector with a size (length) of 1
   and set its single component to zero... the
   RIGHT WAY */
vector_t *vector_new() {
	/* Declare what this function will return */
	vector_t *retval;

	/* First, we need to allocate memory on the heap for the struct */
	retval = /* YOUR CODE HERE */(vector_t*)malloc(sizeof(vector_t));

	/* Check our return value to make sure we got memory */
	if (/* YOUR CODE HERE */retval == NULL) {
		allocation_failed();
	}

	/* Now we need to initialize our data.
	   Since retval->data should be able to dynamically grow,
	   what do you need to do? */
	retval->size = /* YOUR CODE HERE */1;
	retval->data = /* YOUR CODE HERE */(int*)malloc(sizeof(int));

	/* Check the data attribute of our vector to make sure we got memory */
	if (/* YOUR CODE HERE */retval->data == NULL) {
		free(retval);				//Why is this line necessary?
        allocation_failed();
	}

	/* Complete the initialization by setting the single component to zero */
	/* YOUR CODE HERE */retval->data[0] = 0;

	/* and return... */
	return retval;
}

/* Return the value at the specified location/component "loc" of the vector */
int vector_get(vector_t *v, size_t loc) {

	/* If we are passed a NULL pointer for our vector, complain about it and exit. */
	if(v == NULL) {
		fprintf(stderr, "vector_get: passed a NULL vector.\n");
        abort();
	}

	/* If the requested location is higher than we have allocated, return 0.
	 * Otherwise, return what is in the passed location.
	 */
	if (loc < /* YOUR CODE HERE */v->size) {
		return /* YOUR CODE HERE */v->data[loc];
	} else {
		return 0;
	}
}

/* Free up the memory allocated for the passed vector.
   Remember, you need to free up ALL the memory that was allocated. */
void vector_delete(vector_t *v) {
	/* YOUR SOLUTION HERE */
	free(v->data);
	free(v);
}



/* Set a value in the vector. If the extra memory allocation fails, call allocation_failed(). */
void vector_set(vector_t *v, size_t loc, int value) {
	/* What do you need to do if the location is greater than the size we have
	 * allocated?  Remember that unset locations should contain a value of 0.
	 */

	/* YOUR SOLUTION HERE */
	if (loc < v->size) {
		v->data[loc] = value;
	} else {
		int *newdata = (int*)realloc(v->data, (loc + 1)*sizeof(int));
		int i = v->size;
		if (newdata == NULL) {
			allocation_failed();
		}
		// now v->data == NULL, v->size unchanged
		while (i < loc) {
			newdata[i] = 0;
			i++;
		}
		newdata[loc] = value;

		v->data = newdata;
		v->size = loc + 1;
	}
}
```
