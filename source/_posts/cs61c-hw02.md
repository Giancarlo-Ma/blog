---
title: cs61c-hw02
date: 2018-10-13 14:07:16
categories:
- CS61C
tags:
- C
- Machine Structure
---

# Goals
The objective of this assignment is to get you familiar with string/pointer manipulation and C code. You'll also likely get lots of good experience debugging with cgdb (or enhance your ability to use print statements).

# Background
grep is a UNIX utility that is used to search for patterns in text files. It's a powerful and versatile tool, and in this assignment you will implement a version that, while simplified, should still be useful.

Your assignment is to complete the implementation of rgrep, our simplified version of grep. rgrep is "restricted" in the sense that the patterns it matches only support a few regular operators (the easier ones). The way rgrep is used is that a pattern is specified on the command line. rgrep then reads lines from its standard input and prints them out on its standard output if and only if the pattern "matches" the line or some subsection of it. For example, we can use rgrep to search for lines that contain filenames that are at least 3 characters long (plus the ".txt") as follows:

```
# show the contents of test_file.in:
$ cat test_file.in

fine.txt
reallylong.txt
abc.txt
s.txt
nope.pdf

$ ./rgrep '...+\.txt' < test_file.in

fine.txt
reallylong.txt
abc.txt
```
What's going on here? rgrep was given the pattern "...+\.txt"; it printed only the lines from its standard input (the contents of test_file.in) that matched this pattern. How can you tell if a line matches the pattern? A line matches a pattern iff the pattern "appears" somewhere inside the line. In the absence of any special operators, seeing if a line matches a pattern reduces to seeing if the pattern occurs as a substring anywhere in the line. So for most characters, their meaning in a pattern is just to match themselves in the target string. However, there are a few special clauses you must implement:

{% asset_img table.png %}

So, here are some examples of patterns and the kind of lines they match:

{% asset_img table2.png %}

These are the only special characters you have to handle. With the exception of the null character that terminates a string, you should not have to handle other characters in any special way. You may assume that your code will not be run against patterns that don't make sense.
