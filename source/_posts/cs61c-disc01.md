---
title: cs61c-disc02
date: 2018-09-22 09:39:44
categories:
- CS61C
tags:
- Machine Structure
- C
---

# Q1 Uncommented Code? Yuck!
The following functions work correctly (note: this does not mean intelligently), but have no comments. Document the code to prevent it from causing further confusion.
```c
/*
*   recursively calculate array first n term sum
*/
int foo(int *arr, size_t n) {
    return n ? arr[0] + foo(arr + 1, n - 1) : 0;
}
```
```c
2. /*
*   By reversed order to calculate the sum when current term is zero(i.e. if there's zero, plus 1).return bitwise invert plus 1 result(i.e. two's complement).The whole function calculate zero's count times -1.
*/
int bar(int *arr, size_t n) {
    int sum = 0, i;
    for (i = n; i > 0; i--) {
        sum += !arr[i - 1];
    }
    return ~sum + 1;
}
```
```c
/*
*   some xor operations, does nothing.
*/
void baz(int x, int y) {
    x = x ^ y;
    y = x ^ y;
    x = x ^ y;
}
```
# Q2 Programming with Pointers
Implement the following functions so that they perform as described in the comment.
```c
/* Swaps the value of two ints outside of this function. */
void swap(int *x, int *y) {
    int temp = *x;
    *x = *y;
    *y = temp;
}
```
```c
/* Increments the value of an int outside of this function by one. */
void increment(int *x) {
    *x += 1;
    // (*x)++;
    // x[0]++;
}
```
```c
/* Returns the number of bytes in a string. Does not use strlen. */
int mystrlen(char* str) {
    int count = 0;
    while (*str++) {
        count++;
    }
    return count;
}
```
# Q3 Problem?
The following code segments may contain logic and syntax errors. Find and correct them.
```c
/* Returns the sum of all the elements in SUMMANDS. */
int sum(int* summands) {
    int sum = 0;
    for (int i = 0; i < sizeof(summands); i++)
        sum += *(summands + i);
    return sum;
}
```
sizeof returns number of bytes in object,so we need to give the array length in parameters.The following is corrected answer.
```c
/* Returns the sum of all the elements in SUMMANDS. */
int sum(int* summands, int len) {
    int sum = 0;
    for (int i = 0; i < len; i++)
        sum += *(summands + i);
    return sum;
}
```
```c
/* Increments all the letters in the string STRING, held in an array of length N.
* Does not modify any other memory which has been previously allocated. */
void increment(char* string, int n) {
    for (int i = 0; i < n; i++)
        *(string + i)++;
}
```
Because of ++ operator has higher precedance than *, So we can simply insert parenthesis around * operator.(i.e. (*(string + i))++);and we can also exploit the association between array and pointer.The follwing are corrected answers.
```c
void increment(char* string, int n) {
    for (int i = 0; string[i] != 0; i++)
        string[i]++;
}
```
```c
void increment(char* string, int n) {
    for (int i = 0; i < n; i++)
        (*(string + i))++;
}
```
```c
/* Copies the string SRC to DST. */
void copy(char* src, char* dst) {
    while (*dst++ = *src++);
}
```
This has no errors.suffix increment first use * operator then execute plus operation,while loop stops until there is a zero which means string end.
