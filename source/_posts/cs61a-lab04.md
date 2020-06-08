---
title: cs61a-lab04
date: 2018-10-08 10:42:20
categories:
- CS61A
tags:
- python
- SICP
---

## Q1: List Indexing
For each of the following lists, what is the list indexing expression that evaluates to 7? For example, if x = [7], then the answer would be x[0]. You can use the interpreter or Python Tutor to experiment with your answers.
```
>>> x = [1, 3, [5, 7], 9]
x[2][1]

>>> x = [[7]]
x[0][0]

>>> x = [3, 2, 1, [9, 8, 7]]
x[3][2]

>>> x = [[3, [5, 7], 9]]
x[0][1][1]
```
<!-- more -->
What would Python display? If you get stuck, try it out in the Python interpreter!
```
>>> lst = [3, 2, 7, [84, 83, 82]]
>>> lst[4]
Error

>>> lst[3][0]
84
```
## Q2: WWPD: Lists?
What would Python display? Try to figure it out before you type it into the interpreter!
```
>>> [x*x for x in range(5)]
[0, 1, 4, 9, 16]

>>> [n for n in range(10) if n % 2 == 0]
[0, 2, 4, 6, 8]

>>> ones = [1 for i in ["hi", "bye", "you"]]
>>> ones + [str(i) for i in [6, 3, 8, 4]]
[1, 1, 1, '6', '3', '8', '4']

>>> [i+5 for i in [n for n in range(1,4)]]
[6, 7, 8]
```
```
>>> [i**2 for i in range(10) if i < 3]
[0, 1, 4]

>>> lst = ['hi' for i in [1, 2, 3]]
>>> print(lst)
['hi', 'hi', 'hi']

>>> lst + [i for i in ['1', '2', '3']]
['hi', 'hi', 'hi', '1', '2', '3']
```
## Q3: If This Not That
Define if_this_not_that, which takes a list of integers i_list and an integer this. For each element in i_list, if the element is larger than this, then print the element. Otherwise, print "that".
```python
def if_this_not_that(i_list, this):
    """Define a function which takes a list of integers `i_list` and an integer
    `this`. For each element in `i_list`, print the element if it is larger
    than `this`; otherwise, print the word "that".

    >>> original_list = [1, 2, 3, 4, 5]
    >>> if_this_not_that(original_list, 3)
    that
    that
    that
    4
    5
    """
    for elem in i_list:
        if elem <= this:
            print("that")
        else:
            print(elem)

# List comprehension version
def if_this_not_that(i_list, this):
    [print(i) if i > this else print('that') for i in i_list]
```
# City Data Abstraction
Say we have an abstract data type for cities. A city has a name, a latitude coordinate, and a longitude coordinate.

Our ADT has one constructor:

- `make_city(name, lat, lon)`: Creates a city object with the given name, latitude, and longitude.
We also have the following selectors in order to get the information for each city:

- `get_name(city)`: Returns the city's name
- `get_lat(city)`: Returns the city's latitude
- `get_lon(city)`: Returns the city's longitude
Here is how we would use the constructor and selectors to create cities and extract their information:
```
>>> berkeley = make_city('Berkeley', 122, 37)
>>> get_name(berkeley)
'Berkeley'
>>> get_lat(berkeley)
122
>>> new_york = make_city('New York City', 74, 40)
>>> get_lon(new_york)
40
```
All of the selector and constructor functions can be found in `city.py`, if you are curious to see how they are implemented. However, the point of data abstraction is that we do not need to know how an abstract data type is implemented, but rather just how we can interact with and use the data type.

## Q4: Distance
We will now implement the function `distance`, which computes the distance between two city objects. Recall that the distance between two coordinate pairs `(x1, y1)` and `(x2, y2)` can be found by calculating the `sqrt` of `(x1 - x2)**2 + (y1 - y2)**2`. We have already imported sqrt for your convenience. Use the latitude and longitude of a city as its coordinates; you'll need to use the selectors to access this info!
```python
from math import sqrt
def distance(city1, city2):
    """
    >>> city1 = make_city('city1', 0, 1)
    >>> city2 = make_city('city2', 0, 2)
    >>> distance(city1, city2)
    1.0
    >>> city3 = make_city('city3', 6.5, 12)
    >>> city4 = make_city('city4', 2.5, 15)
    >>> distance(city3, city4)
    5.0
    """
    "*** YOUR CODE HERE ***"
    return sqrt((get_lat(city1) - get_lat(city2)) ** 2 + (get_lon(city1) - get_lon(city2)) ** 2)
```

## Q5: Closer city
Next, implement `closer_city`, a function that takes a latitude, longitude, and two cities, and returns the name of the city that is relatively closer to the provided latitude and longitude.

You may only use the selectors and constructors introduced above and the `distance` function you just defined for this question.

*Hint: How can use your distance function to find the distance between the given location and each of the given cities?*
```python
def closer_city(lat, lon, city1, city2):
    """
    Returns the name of either city1 or city2, whichever is closest to
    coordinate (lat, lon).

    >>> berkeley = make_city('Berkeley', 37.87, 112.26)
    >>> stanford = make_city('Stanford', 34.05, 118.25)
    >>> closer_city(38.33, 121.44, berkeley, stanford)
    'Stanford'
    >>> bucharest = make_city('Bucharest', 44.43, 26.10)
    >>> vienna = make_city('Vienna', 48.20, 16.37)
    >>> closer_city(41.29, 174.78, bucharest, vienna)
    'Bucharest'
    """
    "*** YOUR CODE HERE ***"
    city3 = make_city(lat, lon)
    if distance(city3, city1) > distance(city3, city2):
        return get_name(city2)
    else:
        return get_name(city1)
```

## Q6: Don't violate the abstraction barrier!
When writing functions that use an ADT, we should use the constructor(s) and selector(s) whenever possible instead of assuming the ADT's implementation. Relying on a data abstraction's underlying implementation is known as violating the abstraction barrier, and we never want to do this!

It's possible that you passed the doctests for `distance` and `closer_city` even if you violated the abstraction barrier. To check whether or not you did so, uncomment the following lines in your `lab04.py` file:
```python
# make_city = lambda name, lat, lon: { 'name': name, 'lat': lat, 'lon': lon }
# get_name = lambda city: city['name']
# get_lat = lambda city: city['lat']
# get_lon = lambda city: city['lon']
```
These statements change the implementation of the city ADT. The nature of the abstraction barrier guarantees that changing the implementation of an ADT shouldn't affect the functionality of any programs that use that ADT, as long as the constructors and selectors were used properly.

Now, rerun your tests for distance and closer_city without changing any of your code:
```
python3 ok -q distance
python3 ok -q closer_city
```
If you've followed the rules and used the constructor and selectors when you should've, the doctests should still pass!

If you passed the Ok tests before uncommenting those lines but not afterward, the fix is simple! Just replace any code that violates the abstraction barrier, i.e. creating a city with a new list object or indexing into a city, with the appropriate constructor or selector.

Make sure that your functions pass the tests with both the first and the second implementations of the City ADT and that you understand why they should work for both before moving on.

# Optional Questions
All these questions can be found in `lab04_extra.py`

## Q7: Flatten
Write a function `flatten` that takes a (possibly deep) list and "flattens" it. For example:
```
>>> lst = [1, [[2], 3], 4, [5, 6]]
>>> flatten(lst)
[1, 2, 3, 4, 5, 6]
```
Hint: you can check if something is a list by using the built-in `type` function. For example,
```
>>> type(3) == list
False
>>> type([1, 2, 3]) == list
True
```
<span style="color:red">I think the question may be thought as only having 3 conditions. First, the lst is empty list, we can only return []. Second, the first element in lst is a list, then we need call flatten recursively to its first element and the rest of the list. Last, the first element in lst is not a list(aka. one item), then we simply concat the first element and the flattened rest of the list.</span>
```python
def flatten(lst):
    """Returns a flattened version of lst.

    >>> flatten([1, 2, 3])     # normal list
    [1, 2, 3]
    >>> x = [1, [2, 3], 4]      # deep list
    >>> flatten(x)
    [1, 2, 3, 4]
    >>> x = [[1, [1, 1]], 1, [1, 1]] # deep list
    >>> flatten(x)
    [1, 1, 1, 1, 1, 1]
    """
    "*** YOUR CODE HERE ***"
    if not list:
        return []
    elif type(lst[0]) == list:
        return flatten(lst[0]) + flatten(lst[1:])
    else:
        return [lst[0]] + flatten(lst[1:])
```

## Q8: Merge
Write a function `merge` that takes 2 sorted lists `lst1` and `lst2`, and returns a new list that contains all the elements in the two lists in sorted order.
<span style="color:red">This iterative style of this problem may be like this, which can be used to the merge process of mergesort algorithm. The main idea here is that keeping the two lists not empty, then we compare the first element of the two lists, and the compare result decides which one we choose to slice. When the loop ends, we must have one list emptyed, then simply concat the rest list to the new list.</span>
```python
def merge(lst1, lst2):
    """Merges two sorted lists.

    >>> merge([1, 3, 5], [2, 4, 6])
    [1, 2, 3, 4, 5, 6]
    >>> merge([], [2, 4, 6])
    [2, 4, 6]
    >>> merge([1, 2, 3], [])
    [1, 2, 3]
    >>> merge([5, 7], [2, 4, 6])
    [2, 4, 5, 6, 7]
    """
    "*** YOUR CODE HERE ***"
    new = []
    while lst1 and lst2:
        if lst1[0] < lst2[0]:
            new += [lst1[0]]
            lst1 = lst1[1:]
        else:
            new += [lst2[0]]
            lst2 = lst2[1:]
    if lst1:
        return new + lst1
    else:
        return new + lst2
```
<span style="color:red">This main idea of recursion is like the flatten function. There're basiclly 4 conditions(while the solution can be shrinked to 3 conditions in the PDF solution). First, lst1 is empty list, then we return lst2. Second, lst2 is empty, then we return lst1. The rest of recursion is comparing the first element of the lst1 and lst2, then according to the result, we recursively call the merge function.</span>
```python
def merge(lst1, lst2):
    """Merges two sorted lists.

    >>> merge([1, 3, 5], [2, 4, 6])
    [1, 2, 3, 4, 5, 6]
    >>> merge([], [2, 4, 6])
    [2, 4, 6]
    >>> merge([1, 2, 3], [])
    [1, 2, 3]
    >>> merge([5, 7], [2, 4, 6])
    [2, 4, 5, 6, 7]
    """
    "*** YOUR CODE HERE ***"
    if not lst1:
        return lst2
    elif not lst2:
        return lst1
    else if lst1[0] < lst2[0]:
        return [lst1[0]] + merge(lst1[1:], lst2)
    else:
        return [lst2[0]] + merge(lst2[1:], lst1)
```

# Connect N
You've probably heard of Connect 4, a two-player game where the players take turns dropping a colored piece from the top of a column in a grid. The piece ends at the last empty spot in this column - that is, as close to the bottom as possible. A player can only put pieces in columns with open spaces.

{% asset_img connect_4.gif %}

The winner is the first player who gets N of their pieces next to each other - either horizontally, vertically or diagonally. The game ends at this point, or as soon as the board is full.

We can generalize this game so that the goal is to connect N pieces instead of just 4. In this section, we will be implementing a command line version of Connect N!

## Building Connect N
Let's build the combat field for players 'X' and 'O'.

In this lab, we will represent the playing board as a list of lists. We call such a list two-dimensional because we can visualize it as a rectangle. For instance, this list:

`[['-', '-', '-', '-'], ['O', 'O', 'O', 'X'], ['X', 'X', 'X', 'O']]`
would represent the following board:
```
- - - -
O O O X
X X X O
```
What does the number of nested lists represent? What about the number of elements in each nested list? When you have made up your mind, you are ready to build the board!

**Notice that just like lists are zero-indexed, our board is zero-indexed.** This means that the columns and rows in the above board would be numbered like this:
```
0  - - - -
1  O O O X
2  X X X O
   0 1 2 3
```
## Q9: Creating an empty board
We are going to use data abstraction as we build our game, so let's start by making the constructors. We will represent an empty spot by the string `'-'`. In `lab04_extra.py`, fill out the constructors.

First, implement the function `create_row`, which returns one empty row in our board according to our abstraction (i.e., a row is one list).

This function should consist of a one-line return statement.

*Hint: You can create a list in one line using a list comprehension.*
```python
def create_row(size):
    """Returns a single, empty row with the given size. Each empty spot is represented by the string '-'.

    >>> create_row(5)
    ['-', '-', '-', '-', '-']
    """
    "*** YOUR CODE HERE ***"
    return ['-' for x in range(size)]
```
Then, use `create_row` to implement `create_board`, which returns a board with the specified dimensions.

This function should consist of a one-line return statement.
```python
def create_board(rows, columns):
    """Returns a board with the given dimensions.

    >>> create_board(3, 5)
    [['-', '-', '-', '-', '-'], ['-', '-', '-', '-', '-'], ['-', '-', '-', '-', '-']]
    """
    "*** YOUR CODE HERE ***"
    return [create_row(columns) for x in range(rows)]
```
## Q10: Updating the board
Over the course of a game, the board will change and we will need to keep our representation of the board up-to-date. To do so, we will be creating a new board that represents the new state of the game every time a piece is played. Implement `replace_elem`, which takes a list, an index, and an element to be placed at that index in the returned new list.

This function should consist of a one-line return statement.
```python
def replace_elem(lst, index, elem):
    """Create and return a new list whose elements are the same as those in LST except at index INDEX, which should contain element ELEM instead.

    >>> old = [1, 2, 3, 4, 5, 6, 7]
    >>> new = replace_elem(old, 2, 8)
    >>> new
    [1, 2, 8, 4, 5, 6, 7]
    >>> new is old   # check that replace_elem outputs a new list
    False
    """
    assert index >= 0 and index < len(lst), 'Index is out of bounds'
    "*** YOUR CODE HERE ***"
    return [lst[i] if i != index else elem for i in range(len(lst))]
    #  return lst[:index] + [elem] + lst[(index + 1):]
```

## Q11: Manipulating pieces
Now that we have the board ready, let's make our selectors! First, we need a way to find out which piece (`'-'`, `'X'` or `'O'`) is at a given position. Implement `get_piece` so it does this.

Note: Because get_piece is a selector, it is allowed to break through the data abstraction barrier. This means that it is aware that the board is implemented as a list and can use list operations to update it. This allows us to abstract away the inner implementation for all of the other functions that both the programmer and other users will use.

This function should consist of a one-line return statement.
```python
def get_piece(board, row, column):
    """Returns the piece at location (row, column) in the board.

    >>> rows, columns = 2, 2
    >>> board = create_board(rows, columns)
    >>> board = put_piece(board, rows, 0, 'X')[1] # Puts piece "X" in column 0 of board and updates board
    >>> board = put_piece(board, rows, 0, 'O')[1] # Puts piece "O" in column 0 of board and updates board
    >>> get_piece(board, 1, 0)
    'X'
    >>> get_piece(board, 1, 1)
    '-'
    """
    "*** YOUR CODE HERE ***"
    return board[row][column]
```
Right now, all spots in our board are empty, so the output of `get_piece` won't be very interesting - and neither will the game. Let's change that! Go ahead and implement `put_piece`, which places the given `player`'s piece in the given `column`. `put_piece` should return a 2-element tuple that contains (`<row index>`, `<new board>`). The first element is the index of the row the piece ends up in, or -1 if the column is already full. The second element is the new board after the piece has been placed. If the column was full then just return the original board.

Assume that the given column is on the board. Remember that you can get pieces in the board by using get_piece. The argument max_rows may be helpful in determining which rows you should check for an empty slot to put the piece in.

Hint: You will probably need to use the `replace_elem` function you wrote above twice to create the new board.
```python
def put_piece(board, max_rows, column, player):
    """Puts PLAYER's piece in the bottommost empty spot in the given column of the board. Returns a tuple of two elements:

        1. The index of the row the piece ends up in, or -1 if the column is full.
        2. The new board

    >>> rows, columns = 2, 2
    >>> board = create_board(rows, columns)
    >>> row, new_board = put_piece(board, rows, 0, 'X')
    >>> row
    1
    >>> row, new_board = put_piece(new_board, rows, 0, 'O')
    >>> row
    0
    >>> row, new_board = put_piece(new_board, rows, 0, 'X')
    >>> row
    -1
    """
    "*** YOUR CODE HERE ***"
    curr_row = max_rows - 1
    while get_piece(board, curr_row, column) != '-' and curr_row >= 0:
        curr_row -= 1
    if curr_row >= 0:
        new_row = replace_item(board[curr_row], column, player)
        new_board = replace_item(board, curr_row, new_row)
        board = new_board
    return curr_row, board
```

# You are now crossing the abstraction barrier!!!
You have now implemented the constructor and selectors as well as ways to modify the attributes of your abstract data type, the board. From now on, you should never need to treat the board as if it were a list. Instead, trust your abstraction barrier and use the functions you have written so far.

## Q12: Making a move
Let's first write a function for players to make a move in the game. This is different from the `put_piece` function above in that `put_piece` assumes that the player gives a valid column number. `make_move` should only place the piece on the board if the given column is actually on the board. It returns a 2-element tuple (row index, board).

If the move is valid, put a piece in the column and return the index of the row the piece ends up in (do you have a function that will help you do this?) as well as the new board. If the move is invalid, `make_move` should return -1 and the original board, unchanged.

The arguments `max_rows` and `max_cols` describe the dimensions of the board and may be useful in determining whether or not a move is valid.
```python
def make_move(board, max_rows, max_cols, col, player):
    """Put player's piece in column COL of the board, if it is a valid move.
    Return a tuple of two values:

        1. If the move is valid, make_move returns the index of the row the piece is placed in. Otherwise, it returns -1.
        2. The updated board

    >>> rows, columns = 2, 2
    >>> board = create_board(rows, columns)
    >>> row, board = make_move(board, rows, columns, 0, 'X')
    >>> row
    1
    >>> get_piece(board, 1, 0)
    'X'
    >>> row, board = make_move(board, rows, columns, 0, 'O')
    >>> row
    0
    >>> row, board = make_move(board, rows, columns, 0, 'X')
    >>> row
    -1
    >>> row, board = make_move(board, rows, columns, -4, '0')
    >>> row
    -1
    """
    "*** YOUR CODE HERE ***"
    if col >= max_cols or col < 0:
        return -1, board
    else:
        return put_piece(board, max_rows, col, player)
```
## Q13: Printing and viewing the board
Wouldn't it be great if we could actually see the board and the pieces on it? Let's now write a function to help us do that.

The function print_board takes in a board (as defined by our abstraction) and the dimensions of the board, and it prints out the current state of the board.

We would like our board to look good, and for this, strings do a better job than lists. Thus, we would like the row ['X', 'X', 'O', '-'] to be printed as 'X X O -' where the pieces are separated by a single blank space. Remember that you can concatenate strings with the + operator, e.g. 'hel' + 'lo' = 'hello'.

Remember that we're still on the other side of the abstraction barrier, and you must implement this function as if we didn't know the board is a list of lists. This is called respecting the data abstraction barrier. Specifically, to get information about board, you should use the selectors you've implemented instead of indexing into it.

*Hint: You might find that you're failing doctests that seem to match your output. Chances are that you have an extra space character at the end of your rows in your board. A function that might come in handy is strip(), which removes leading and trailing whitespace from a string. For example:*
```
>>> s = '   hello '
>>> s.strip()
'hello'
```
<span style="color:red">My solution</span>
```python
def print_board(board, max_rows, max_cols):
    """Prints the board. Row 0 is at the top, and column 0 at the far left.

    >>> rows, columns = 2, 2
    >>> board = create_board(rows, columns)
    >>> print_board(board, rows, columns)
    - -
    - -
    >>> new_board = make_move(board, rows, columns, 0, 'X')[1]
    >>> print_board(new_board, rows, columns)
    - -
    X -
    """
    "*** YOUR CODE HERE ***"
    row = 0
    while row < max_rows:
        col = 0
        row_str = ''
        while col < max_cols:
            row_str += get_piece(board, row, col) + ' '
            col += 1
        row_str = row_str.strip()
        print(row_str)
        row += 1
```
<span style="color:red">PDF solution</span>
```python
def print_board(board, max_rows, max_cols):
    """Prints the board. Row 0 is at the top, and column 0 at the far left.

    >>> rows, columns = 2, 2
    >>> board = create_board(rows, columns)
    >>> print_board(board, rows, columns)
    - -
    - -
    >>> new_board = make_move(board, rows, columns, 0, 'X')[1]
    >>> print_board(new_board, rows, columns)
    - -
    X -
    """
    for row in range(max_rows):
        row_str = ''
        for col in range(max_cols):
            piece = get_piece(board, row, col)
            row_str += piece + " "
        print(row_str.strip())
```

## Q14: Checking for victory
Fun, right? At long as you don't care about winning... The last thing we need for our Connect N game to be fully functioning is the ability to detect a win.

First, let's implement two helper functions `check_win_row` and `check_win_col` that check for horizontal and vertical wins for the given player.

Since we check for wins after each turn, and only the player who made the most recent move can have a win, `check_win_row` and `check_win_col` should only check for a win for the player that is passed as an argument. Also remember that `num_connect` tells you how many adjacent pieces are needed for a win. The arguments `max_rows` and `max_cols` describe the dimensions of the game board.

As in `print_board`, use the data abstractions you just built.
```python
def check_win_row(board, max_rows, max_cols, num_connect, row, player):
    """ Returns True if the given player has a horizontal win
    in the given row, and otherwise False.

    >>> rows, columns, num_connect = 4, 4, 2
    >>> board = create_board(rows, columns)
    >>> board = make_move(board, rows, columns, 0, 'X')[1]
    >>> board = make_move(board, rows, columns, 0, 'O')[1]
    >>> check_win_row(board, rows, columns, num_connect, 3, 'O')
    False
    >>> board = make_move(board, rows, columns, 2, 'X')[1]
    >>> board = make_move(board, rows, columns, 0, 'O')[1]
    >>> check_win_row(board, rows, columns, num_connect, 3, 'X')
    False
    >>> board = make_move(board, rows, columns, 1, 'X')[1]
    >>> check_win_row(board, rows, columns, num_connect, 3, 'X')
    True
    >>> check_win_row(board, rows, columns, 4, 3, 'X')    # A win depends on the value of num_connect
    False
    >>> check_win_row(board, rows, columns, num_connect, 3, 'O')   # We only detect wins for the given player
    False
    """
    "*** YOUR CODE HERE ***"
    count = 0
    for col in range(max_cols):
        if get_piece(board, row, col) == player:
            count += 1
            if count >= num_connect:
                return True
        else:
            count = 0
    return False
```
```python
def check_win_column(board, max_rows, max_cols, num_connect, col, player):
    """ Returns True if the given player has a vertical win in the given column,
    and otherwise False.

    >>> rows, columns, num_connect = 5, 5, 2
    >>> board = create_board(rows, columns)
    >>> board = make_move(board, rows, columns, 0, 'X')[1]
    >>> board = make_move(board, rows, columns, 1, 'O')[1]
    >>> check_win_column(board, rows, columns, num_connect, 0, 'X')
    False
    >>> board = make_move(board, rows, columns, 1, 'X')[1]
    >>> board = make_move(board, rows, columns, 1, 'O')[1]
    >>> check_win_column(board, rows, columns, num_connect, 1, 'O')
    False
    >>> board = make_move(board, rows, columns, 2, 'X')[1]
    >>> board = make_move(board, rows, columns, 1, 'O')[1]
    >>> check_win_column(board, rows, columns, num_connect, 1, 'O')
    True
    >>> check_win_column(board, rows, columns, 4, 1, 'O')
    False
    >>> check_win_column(board, rows, columns, num_connect, 1, 'X')
    False
    """
    "*** YOUR CODE HERE ***"
    count = 0
    for row in range(max_row):
        if get_piece(board, row, col) == player:
            count += 1
            if count >= num_connect:
                return True
        else:
            count = 0
    return False
```
## Q15: Winning!
Finally, let's implement a way to check for any wins. Implement `check_win` so that it returns `True` if there is a win in any direction - that is, horizontally, vertically or diagonally.

You should use the functions you just wrote, `check_win_row` and `check_win_column`, along with the provided function `check_win_diagonal(board, max_rows, max_cols, num_connect, row, col, player)`, which returns `True` if the given player has a diagonal win passing the spot (row, column) and `False` otherwise.
```python
def check_win(board, max_rows, max_cols, num_connect, row, col, player):
    """Returns True if the given player has any kind of win passing through
    (row, col), and False otherwise.

    >>> rows, columns, num_connect = 2, 2, 2
    >>> board = create_board(rows, columns)
    >>> board = make_move(board, rows, columns, 0, 'X')[1]
    >>> board = make_move(board, rows, columns, 1, 'O')[1]
    >>> board = make_move(board, rows, columns, 0, 'X')[1]
    >>> check_win(board, rows, columns, num_connect, 0, 0, 'O')
    False
    >>> check_win(board, rows, columns, num_connect, 0, 0, 'X')
    True

    >>> board = create_board(rows, columns)
    >>> board = make_move(board, rows, columns, 0, 'X')[1]
    >>> board = make_move(board, rows, columns, 0, 'O')[1]
    >>> board = make_move(board, rows, columns, 1, 'X')[1]
    >>> check_win(board, rows, columns, num_connect, 1, 0, 'X')
    True
    >>> check_win(board, rows, columns, num_connect, 0, 0, 'X')
    False

    >>> board = create_board(rows, columns)
    >>> board = make_move(board, rows, columns, 0, 'X')[1]
    >>> board = make_move(board, rows, columns, 1, 'O')[1]
    >>> board = make_move(board, rows, columns, 1, 'X')[1]
    >>> check_win(board, rows, columns, num_connect, 0, 0, 'X')
    False
    >>> check_win(board, rows, columns, num_connect, 1, 0, 'X')
    True
    """
    diagonal_win = check_win_diagonal(board, max_rows, max_cols, num_connect, row, col, player)
    "*** YOUR CODE HERE ***"
    return check_win_row(board, max_rows, max_cols, num_connect, row, player) or check_win_column(board, max_rows, max_cols, num_connect, col, player) or diagonal_win
```
