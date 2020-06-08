---
title: cs61c-disc02
date: 2018-10-11 09:39:44
categories:
- CS61C
tags:
- Machine Structure
- C
---

#  Q1 C Memory Management
In which memory sections (CODE, STATIC, HEAP, STACK) do the following reside?
```c
#define C 2
const int val = 16;
char arr[] = "foo";
void foo(int arg){
    char *str = (char *) malloc (C*val);
    char *ptr = arr;
}
```

- arg resides in STACK, str resides in STACK
- arr resides in STATIC, \*str resides in HEAP
- val resides in CODE, C resides CODE

What is wrong with the C code below?
```c
int* ptr = malloc(4 * sizeof(int));
if(extra_large) ptr = malloc(10 * sizeof(int));
return ptr;
```
<!-- more -->
Memory leak if extra_large is TRUE.

Write code to prepend (add to the start) to a linked list, and to free/empty the entire list.
```c
struct ll_node {
    struct ll_node* next;
    int value;
}
void free_ll(struct ll_node** list){
    if (*list == NULL) {
        return;
    }
    free_ll(&((*list) -> next));
    free(*list);
    *list = NULL;
}

void prepend(struct ll_node** list, int value){
    struct ll_node *ll_p = (struct ll_node *)malloc(sizeof(struct ll_node));
    if (ll_p == NULL) {
        printf("memory exhausted\n");exit(1);
    }
    ll_p -> value = value;
    ll_p -> next = *list;
    *list = ll_p;
}
```
*Note: list points to the first element of the list, or to NULL if the list is empty*

# 2 MIPS Intro

{% asset_img mips.png %}

a) make arr[2] <- 4
b) make $t0 <- 0
c) alignment error
d) alignment error
e) out of bounds
f) make arr[3] <- 6

In question 1, what other instructions could be used in place of each load/store without alignment errors?

<span style="color:red">In a, we can use lb, lh because the number is 4, which in 2's complement is 00000100 where the rest is all zero. sw can be replaced by sb and sh also.</span>
<span style="color:red">In b, lh can be replaced by lb and lw, the value to be transfered is zero.</span>
<span style="color:red">In e, sw can be replaced by sh, sb. The value is 8.</span>
<span style="color:red">In f, sw can be replaced by sh, sb because the value is 6</span>

{% asset_img mips1.png %}

# 3 Translating between C and MIPS

Translate between the C and MIPS code. You may want to use the MIPS Green Sheet as a reference. In all of the C examples, we show you how the different variables map to registers – you don’t have to worry about the stack or any memory-related issues.

{% asset_img mips2.png %}
