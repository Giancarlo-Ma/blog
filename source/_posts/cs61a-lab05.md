---
title: cs61a-lab05
date: 2018-10-08 22:17:26
categories:
- CS61A
tags:
- python
- SICP
---

## Q1: Acorn Finder
The squirrels on campus need your help! There are a lot of trees on campus and the squirrels would like to know which ones contain acorns. Define the function acorn_finder, which takes in a tree and returns True if the tree contains a node with the value 'acorn' and False otherwise.
<!-- more -->
```python
def acorn_finder(t):
    """Returns True if t contains a node with the value 'acorn' and
    False otherwise.

    >>> scrat = tree('acorn')
    >>> acorn_finder(scrat)
    True
    >>> sproul = tree('roots', [tree('branch1', [tree('leaf'), tree('acorn')]), tree('branch2')])
    >>> acorn_finder(sproul)
    True
    >>> numbers = tree(1, [tree(2), tree(3, [tree(4), tree(5)]), tree(6, [tree(7)])])
    >>> acorn_finder(numbers)
    False
    """
    "*** YOUR CODE HERE ***"
    if label(t) == 'acorn':
        return True
    for b in branches(t):
        if acorn_finder(b):
            return True
    return False

# Alternative solution
def acorn_finder(t):
    if label(t) == 'acorn':
        return True
    return True in [acorn_finder(b) for b in branches(t)]
```

## Q2: Pruning Leaves
Define a function `prune_leaves` that given a tree `t` and a tuple of values `vals`, produces a version of `t` with all its leaves that are in `vals` removed. Do not attempt to try to remove non-leaf nodes and do not remove leaves that do not match any of the items in `vals`. Return `None` if pruning the tree results in there being no nodes left in the tree.
```python
def prune_leaves(t, vals):
    """Return a modified copy of t with all leaves that have a label
    that appears in vals removed.  Return None if the entire tree is
    pruned away.

    >>> t = tree(2)
    >>> print(prune_leaves(t, (1, 2)))
    None
    >>> numbers = tree(1, [tree(2), tree(3, [tree(4), tree(5)]), tree(6, [tree(7)])])
    >>> print_tree(numbers)
    1
      2
      3
        4
        5
      6
        7
    >>> print_tree(prune_leaves(numbers, (3, 4, 6, 7)))
    1
      2
      3
        5
      6
    """
    "*** YOUR CODE HERE ***"
    if is_leaf(t) and (label(t) in vals):
        return None
    new_branches = []
    for b in branches(t):
        new_branch = prune_leaves(b, vals)
        if new_branch:
            new_branches += [new_branch]
    return tree(label(t), new_branches)
```
## Q3: Memory
Write a function that takes in a number n and returns a one-argument function. The returned function takes in a function that is used to update n. It prints the updated n value. (Note that this is different from a commentary function from hog since it doesn't return a new function)
```python
def memory(n):
    """
    >>> f = memory(10)
    >>> f(lambda x: x * 2)
    20
    >>> f(lambda x: x - 7)
    13
    >>> f(lambda x: x > 5)
    True
    """
    "*** YOUR CODE HERE ***"
    def f(g):
        nonlocal n
        n = g(n)
        print(n)
    return f
```
# Shakespeare and Dictionaries
We will use dictionaries to approximate the entire works of Shakespeare! We're going to use a bigram language model. Here's the idea: We start with some word -- we'll use "The" as an example. Then we look through all of the texts of Shakespeare and for every instance of "The" we record the word that follows "The" and add it to a list, known as the successors of "The". Now suppose we've done this for every word Shakespeare has used, ever.

Let's go back to "The". Now, we randomly choose a word from this list, say "cat". Then we look up the successors of "cat" and randomly choose a word from that list, and we continue this process. This eventually will terminate in a period (".") and we will have generated a Shakespearean sentence!

The object that we'll be looking things up in is called a "successor table", although really it's just a dictionary. The keys in this dictionary are words, and the values are lists of successors to those words.

## Q4: Successor Tables
Here's an incomplete definition of the `build_successors_table` function. The input is a list of words (corresponding to a Shakespearean text), and the output is a successors table. (By default, the first word is a successor to "."). See the example below.
```python
def build_successors_table(tokens):
    """Return a dictionary: keys are words; values are lists of successors.

    >>> text = ['We', 'came', 'to', 'investigate', ',', 'catch', 'bad', 'guys', 'and', 'to', 'eat', 'pie', '.']
    >>> table = build_successors_table(text)
    >>> sorted(table)
    [',', '.', 'We', 'and', 'bad', 'came', 'catch', 'eat', 'guys', 'investigate', 'pie', 'to']
    >>> table['to']
    ['investigate', 'eat']
    >>> table['pie']
    ['.']
    >>> table['.']
    ['We']
    """
    table = {}
    prev = '.'
    for word in tokens:
        if prev not in table:
            "*** YOUR CODE HERE ***"
            table[prev] = []
        "*** YOUR CODE HERE ***"
        table[prev] += [word]
        prev = word
    return table
```
## Q5: Construct the Sentence
Let's generate some sentences! Suppose we're given a starting word. We can look up this word in our table to find its list of successors, and then randomly select a word from this list to be the next word in the sentence. Then we just repeat until we reach some ending punctuation.

*Hint: to randomly select from a list, import the Python random library with `import random` and use the expression `random.choice(my_list)`*

This might not be a bad time to play around with adding strings together as well. Let's fill in the construct_sent function!
```python
def construct_sent(word, table):
    """Prints a random sentence starting with word, sampling from
    table.

    >>> table = {'Wow': ['!'], 'Sentences': ['are'], 'are': ['cool'], 'cool': ['.']}
    >>> construct_sent('Wow', table)
    'Wow!'
    >>> construct_sent('Sentences', table)
    'Sentences are cool.'
    """
    import random
    result = ''
    while word not in ['.', '!', '?']:
        "*** YOUR CODE HERE ***"
        result += word
        word = random.choice(table[word])
    return result.strip() + word
```
## Putting it all together
Great! Now let's try to run our functions with some actual data. The following snippet included in the skeleton code will return a list containing the words in all of the works of Shakespeare.

*Warning: Do NOT try to print the return result of this function.*
```python
def shakespeare_tokens(path='shakespeare.txt', url='http://composingprograms.com/shakespeare.txt'):
    """Return the words of Shakespeare's plays as a list."""
    import os
    from urllib.request import urlopen
    if os.path.exists(path):
        return open('shakespeare.txt', encoding='ascii').read().split()
    else:
        shakespeare = urlopen(url)
        return shakespeare.read().decode(encoding='ascii').split()
```

Uncomment the following two lines to run the above function and build the successors table from those tokens.
```python
# Uncomment the following two lines
# tokens = shakespeare_tokens()
# table = build_successors_table(tokens)
```
Next, let's define a utility function that constructs sentences from this successors table:
```
>>> def sent():
...     return construct_sent('The', table)
>>> sent()
" The plebeians have done us must be news-cramm'd."

>>> sent()
" The ravish'd thee , with the mercy of beauty!"

>>> sent()
" The bird of Tunis , or two white and plucker down with better ; that's God's sake."
```
Notice that all the sentences start with the word "The". With a few modifications, we can make our sentences start with a random word. The following random_sent function (defined in your starter file) will do the trick:
```python
def random_sent():
    import random
    return construct_sent(random.choice(table['.']), table)
```
Go ahead and load your file into Python (be sure to use the `-i` flag). You can now call the `random_sent` function to generate random Shakespearean sentences!
```
>>> random_sent()
' Long live by thy name , then , Dost thou more angel , good Master Deep-vow , And tak'st more ado but following her , my sight Of speaking false!'

>>> random_sent()
' Yes , why blame him , as is as I shall find a case , That plays at the public weal or the ghost.'
```

# More Trees Practice
## Q6: Sprout leaves
Define a function `sprout_leaves` that takes in a tree, `t`, and a list of values, `vals`. It produces a new tree that is identical to `t`, but where each old leaf node has new branches, one for each value in `vals`.

For example, say we have the tree `t = tree(1, [tree(2), tree(3, [tree(4)])])`:
```
  1
 / \
2   3
    |
    4
```
If we call `sprout_leaves(t, [5, 6])`, the result is the following tree:
```
       1
     /   \
    2     3
   / \    |
  5   6   4
         / \
        5   6
```
```python
def sprout_leaves(t, vals):
    """Sprout new leaves containing the data in vals at each leaf in
    the original tree t and return the resulting tree.

    >>> t1 = tree(1, [tree(2), tree(3)])
    >>> print_tree(t1)
    1
      2
      3
    >>> new1 = sprout_leaves(t1, [4, 5])
    >>> print_tree(new1)
    1
      2
        4
        5
      3
        4
        5

    >>> t2 = tree(1, [tree(2, [tree(3)])])
    >>> print_tree(t2)
    1
      2
        3
    >>> new2 = sprout_leaves(t2, [6, 1, 2])
    >>> print_tree(new2)
    1
      2
        3
          6
          1
          2
    """
    "*** YOUR CODE HERE ***"
    if is_leaf(t):
        return tree(label(t), [tree(v) for v in vals])
    return tree(label(t), [sprout_leaves(b, vals) for b in branches(t)])
```
## Q7: Add trees
Define the function `add_trees`, which takes in two trees and returns a new tree where each corresponding node from the first tree is added with the node from the second tree. If a node at any particular position is present in one tree but not the other, it should be present in the new tree as well.

*Hint: You may want to use the built-in zip function to iterate over multiple sequences at once.

Note: If you feel that this one's a lot harder than the previous tree problems, that's totally fine! This is a pretty difficult problem, but you can do it! Talk about it with other students, and come back to it if you need to.*
```python
def add_trees(t1, t2):
    """
    >>> numbers = tree(1,
    ...                [tree(2,
    ...                      [tree(3),
    ...                       tree(4)]),
    ...                 tree(5,
    ...                      [tree(6,
    ...                            [tree(7)]),
    ...                       tree(8)])])
    >>> print_tree(add_trees(numbers, numbers))
    2
      4
        6
        8
      10
        12
          14
        16
    >>> print_tree(add_trees(tree(2), tree(3, [tree(4), tree(5)])))
    5
      4
      5
    >>> print_tree(add_trees(tree(2, [tree(3)]), tree(2, [tree(3), tree(4)])))
    4
      6
      4
    >>> print_tree(add_trees(tree(2, [tree(3, [tree(4), tree(5)])]), \
    tree(2, [tree(3, [tree(4)]), tree(5)])))
    4
      6
        8
        5
      5
    """
    "*** YOUR CODE HERE ***"
    if not t1:
        return t2
    if not t2:
        return t1
    new_label = label(t1) + label(t2)
    t1_children, t2_children = branches(t1), branches(t2)
    length_t1, length_t2 = len(t1_children), len(t2_children)
    if length_t1 < length_t2:
        t1_children += [None for _ in range(length_t1, length_t2)]
    elif len(t1_children) > len(t2_children):
        t2_children += [None for _ in range(length_t2, length_t1)]
    return tree(new_label, [add_trees(child1, child2) for child1, child2 in zip(t1_children, t2_children)])
```
