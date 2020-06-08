---
title: cs61a-lab06
date: 2018-10-19 21:14:07
categories:
- CS61A
tags:
- SICP
- python
---

# Lab Check-Off
## Q1: To Tree or Not to Tree
Sign up for checkoffs in your lab if you'd like to get credit for this week's checkoff.
Say you have some function defined as follows:
```python
>>> def sum_tree(t):
...     """Computes the sum of all the nodes in a tree"""
...     if is_leaf(t):
...         return t
...     else:
...         return t + sum([sum_tree(b) for b in branches(t)])
```
<!-- more -->
Does this work? Why or why not? Some key points to think about:

- What type of data are we trying to return?
- Are we passing in the correct type of data into sum_tree in each recursive call?
- What kind of data is t?

We need to return the sum of the labels of tree, which is obviously a number.

# Object-Oriented Programming
In this lab we'll be diving into **object-oriented programming** (OOP), a model of programming that allows you to think of data in terms of "objects" with their own characteristics and actions, just like objects in real life! This is very powerful and allows you to create objects that are specific to your program - you can read up on all the details here.

# OOP Example: Car Class
Professor DeNero is running late, and needs to get from San Francisco to Berkeley before lecture starts. He'd take BART, but that will take too long. It'd be great if he had a car. A monster truck would be best, but a car will do -- for now...

In `car.py`, you'll find a class called `Car`. A **class** is a blueprint for creating objects of that type. In this case, the `Car` class statement tells us how to create `Car` objects.
## Constructor
Let's build Professor DeNero a car! Don't worry, you won't need to do any physical work -- the **constructor** will do it for you. The **constructor** of a class is a function that creates an **instance**, or a single occurrence, of the object outlined by the class. In Python, the constructor method is named __init__. Note that there must be two underscores on each side of init. The Car class' constructor looks like this:
```python
def __init__(self, make, model):
    self.make = make
    self.model = model
    self.color = 'No color yet. You need to paint me.'
    self.wheels = Car.num_wheels
    self.gas = Car.gas
```
The `__init__` method for `Car` has three parameters. The first one, `self`, is automatically bound to the newly created `Car` object. The second and third parameters, `make` and `model`, are bound to the arguments passed to the constructor, meaning when we make a `Car` object, we must provide two arguments. Don't worry about the code inside the body of the constructor for now.

Let's make our car. Professor DeNero would like to drive a Tesla Model S to lecture. We can construct an instance of `Car` with `'Tesla'` as the make and `'Model S'` as the `model`. Rather than calling `__init__` explicitly, Python allows us to make an instance of a class by using the name of the class.
```
>>> deneros_car = Car('Tesla', 'Model S')
```
Here, `'Tesla'` is passed in as the `make`, and `'Model S'` as the `model`. Note that we don't pass in an argument for `self`, since its value is always the object being created. An `object` is an instance of a class. In this case, `deneros_car` is now bound to a `Car` object or, in other words, an instance of the `Car` class.

## Attributes
So how are the `make` and `model` of Professor DeNero's car actually stored? Let's talk about **attributes** of instances and classes. Here's a snippet of the code in `car.py` with the instance and class attributes in the `Car` class:
```python
class Car(object):
    num_wheels = 4
    gas = 30
    headlights = 2
    size = 'Tiny'

    def __init__(self, make, model):
        self.make = make
        self.model = model
        self.color = 'No color yet. You need to paint me.'
        self.wheels = Car.num_wheels
        self.gas = Car.gas

    def paint(self, color):
        self.color = color
        return self.make + ' ' + self.model + ' is now ' + color

    def drive(self):
        if self.wheels < Car.num_wheels or self.gas <= 0:
            return self.make + ' ' + self.model + ' cannot drive!'
        self.gas -= 10
        return self.make + ' ' + self.model + ' goes vroom!'

    def pop_tire(self):
        if self.wheels > 0:
            self.wheels -= 1

    def fill_gas(self):
        self.gas += 20
        return self.make + ' ' + self.model + ' gas level: ' + str(self.gas)
```
In the first two lines of the constructor, the name `self.make` is bound to the first argument passed to the constructor and `self.model` is bound to the second. These are two examples of instance attributes. An **instance attribute** is a quality or variable that is specific to an instance, and not the class itself! Instance attributes are accessed using dot notation (separating the instance and attribute with a period) with an instance. In this case, self is bound to our instance, so `self.model` references our instance's model.

Our car has other instance attributes too, like `color` and `wheels`. As instance attributes, the make, model, and color of `deneros_car` do not affect the make, model, and color of other cars.

On the other hand, a **class attribute** is a quality that is shared among all instances of the class. For example, the `Car` class has four class attributes defined at the beginning of a class: `num_wheels = 4`, `gas = 30`, `headlights = 2` and s`ize = 'Tiny'`. The first says that all cars have `4` wheels.

You might notice in the `__init__` method of the `Car` class, the instance attribute `gas` is initialized to the value of `Car.gas`, the class attribute. Why don't we just use the class attribute, then? The reason is because each `Car`'s `gas` attribute needs to be able to change independently of each other. If one `Car` drives for a while, it should use up some `gas`, and that `Car` instance should reflect that by having a lower `gas` value. However, all other `Car`s shouldn't lose any `gas`, and changes to a class attribute will affect all instances of the class.

### Dot Notation
Class attributes can also be accessed using dot notation, both on an instance and on the class name itself. For example, we can access the class attribute `size` of `Car` like this:
```
>>> Car.size
'Tiny'
```
And in the following line, we access deneros_car's color attribute:
```
>>> deneros_car.color
'No color yet. You need to paint me.'
```
Looks like we need to paint deneros_car!

## Methods
Let's use the `paint` method from the `Car` class. Methods are functions that are specific to a class; only an instance of the class can use them. We've already seen one method: `__init__`! Think of methods as actions or abilities of objects. How do we call methods on an instance? You guessed it, dot notation!
```
>>> deneros_car.paint('black')
'Tesla Model S is now black'
>>> deneros_car.color
'black'
```
Awesome! But if you take a look at the `paint` method, it takes two parameters. So why don't we need to pass two arguments? Just like we've seen with `__init__`, all methods of a class have a `self` parameter to which Python automatically binds the instance that is calling that method. Here, `deneros_car` is bound to `self` so that the body of `paint` can access its attributes!

You can also call methods using the class name and dot notation; for example,
```
>>> Car.paint(deneros_car, 'red')
'Tesla Model S is now red'
```
Notice that unlike when we painted Professor DeNero's car black, this time we had to pass in two arguments: one for `self` and one for `color`. This is because when you call a method using dot notation from an instance, Python knows what instance to automatically bind to `self`. However, when you call a method using dot notation from the class, Python doesn't know which instance of `Car` we want to paint, so we have to pass that in as well.

## Inheritance
Professor DeNero's red Tesla is pretty cool, but he wants a bigger car! How about we create a monster truck for him instead? In `car.py`, we've defined a `MonsterTruck` class. Let's look at the code for `MonsterTruck`:
```python
class MonsterTruck(Car):
    size = 'Monster'

    def rev(self):
        print('Vroom! This Monster Truck is huge!')

    def drive(self):
        self.rev()
        return Car.drive(self)
```
Wow! The truck may be big, but the source code is tiny! Let's make sure that the truck still does what we expect it to do. Let's create a new instance of Professor DeNero's monster truck:
```
>>> deneros_truck = MonsterTruck('Monster Truck', 'XXL')
```
Does it behave as you would expect a `Car` to? Can you still paint it?
Is it even drivable?

Well, the class `MonsterTruck` is defined as `class MonsterTruck(Car)`:, meaning its superclass is `Car`. Likewise, the class `MonsterTruck` is a subclass of the` Car` class. That means the `MonsterTruck` class inherits all the attributes and methods that were defined in `Car`, including its constructor!

**Inheritance** makes setting up a hierarchy of classes easier because the amount of code you need to write to define a new class of objects is reduced. You only need to add (or override) new attributes or methods that you want to be unique from those in the superclass.
```
>>> deneros_car.size
'Tiny'
>>> deneros_truck.size
'Monster'
```
Wow, what a difference in size! This is because the class attribute `size` of `MonsterTruck` overrides the `size` class attribute of `Car`, so all `MonsterTruck` instances are '`Monster'`-sized.

In addition, the `drive` method in `MonsterTruck` overrides the one in `Car`. To show off all `MonsterTruck` instances, we defined a `rev` method specific to `MonsterClass`. Regular `Cars` cannot `rev`! Everything else -- the constructor `__init__`, `paint`, `num_wheels`, `gas` -- are inherited from `Car`.

# Magic: The lambda-ing
In the next part of this lab, we will be implementing a card game!

You can start the game by typing:
```
python3 cardgame.py
```
**This game doesn't work yet**. If we run this right now, the code will error, since we haven't implemented anything yet. When it's working, you can exit the game and return to the command line with `Ctrl-C` or `Ctrl-D`.

This game uses several different files.

- Code for all the questions in this lab can be found in `classes.py`.
Some utility for the game can be found in `cardgame.py`, but you won't need to open or read this file. This file doesn't actually mutate any instances directly - instead, it calls methods of the different classes, maintaining a strict abstraction barrier.
- If you want to modify your game later to add your own custom cards and decks, you can look in `cards.py` to see all the standard cards and the default deck; here, you can add more cards and change what decks you and your opponent use. The cards were not created with balance in mind, so feel free to modify the stats and add/remove cards as desired.
**Rules of the Game** This game is a little involved, though not nearly as much as its namesake. Here's how it goes:

There are two players. Each player has a hand of cards and a deck, and at the start of each round, each player draws a card from their deck. If a player's deck is empty when they try to draw, they will automatically lose the game. Cards have a name, an attack stat, and a defense stat. Each round, each player chooses one card to play from their own hands. The card with the higher power wins the round. Each played card's power value is calculated as follows:
```
(player card's attack) - (opponent card's defense) / 2
```
For example, let's say Player 1 plays a card with 2000 ATK/1000 DEF and Player 2 plays a card with 1500 ATK/3000 DEF. Their cards' powers are calculated as:
```
P1: 2000 - 3000/2 = 2000 - 1500 = 500
P2: 1500 - 1000/2 = 1500 - 500 = 1000
```
so Player 2 would win this round.

The first player to win 8 rounds wins the match!

However, there are a few effects we can add (in the optional questions section) to make this game a bit more interesting. Cards are split into Tutor, TA, and Professor types, and each type has a different effect when they're played. All effects are applied before power is calculated during that round:

- A Tutor will cause the opponent to discard and re-draw the first 3 cards in their hand.
- A TA will swap the opponent card's attack and defense.
- A Professor adds the opponent card's attack and defense to all cards in their deck and then remove all cards in the opponent's deck that share its attack or defense!
These are a lot of rules to remember, so refer back here if you need to review them, and let's start making the game

## Q3: Making Cards
To play a card game, we're going to need to have cards, so let's make some! We're gonna implement the basics of the `Card` class first.

First, implement the `Card` class constructor in `classes.py`. This constructor takes three arguments:

- the `name` of the card, a string
- the `attack` stat of the card, an integer
- the `defense` stat of the card, an integer
Each `Card` instance should keep track of these values using instance attributes called `name`, `attack`, and `defense`.

You should also implement the `power` method in `Card`, which takes in another card as an input and calculates the current card's power. Check the Rules section if you want a refresher on how power is calculated.
```python
class Card(object):
    cardtype = 'Staff'

    def __init__(self, name, attack, defense):
        """
        Create a Card object with a name, attack,
        and defense.
        >>> staff_member = Card('staff', 400, 300)
        >>> staff_member.name
        'staff'
        >>> staff_member.attack
        400
        >>> staff_member.defense
        300
        >>> other_staff = Card('other', 300, 500)
        >>> other_staff.attack
        300
        >>> other_staff.defense
        500
        """
        "*** YOUR CODE HERE ***"
        self.name = name
        self.attack = attack
        self.defense = defense

    def power(self, other_card):
        """
        Calculate power as:
        (player card's attack) - (opponent card's defense)/2
        where other_card is the opponent's card.
        >>> staff_member = Card('staff', 400, 300)
        >>> other_staff = Card('other', 300, 500)
        >>> staff_member.power(other_staff)
        150.0
        >>> other_staff.power(staff_member)
        150.0
        >>> third_card = Card('third', 200, 400)
        >>> staff_member.power(third_card)
        200.0
        >>> third_card.power(staff_member)
        50.0
        """
        "*** YOUR CODE HERE ***"
        return self.attack - (other_card.defense) / 2
```
## Q4: Making a Player
Now that we have cards, we can make a deck, but we still need players to actually use them. We'll now fill in the implementation of the `Player` class.
A `Player` instance has three instance attributes:

- `name` is the player's name. When you play the game, you can enter your name, which will be converted into a string to be passed to the constructor.
- `deck` is an instance of the `Deck` class. You can draw from it using its `.draw()` method.
- `hand` is a list of `Card` instances. Each player should start with 5 cards in their hand, drawn from their `deck`. Each card in the hand can be selected by its index in the list during the game. When a player draws a new card from the deck, it is added to the end of this list.
Complete the implementation of the constructor for `Player` so that `self.hand` is set to a list of 5 cards drawn from the player's deck.

Next, implement the `draw` and `play` methods in the `Player` class. The `draw` method draws a card from the deck and adds it to the player's hand. The `play` method removes and returns a card from the player's hand at the given index.

Call `deck.draw()` when implementing `Player.__init__` and `Player.draw`. Don't worry about how this function works - leave it all to the abstraction!
```python
class Player(object):
    def __init__(self, deck, name):
        """Initialize a Player object.
        A Player starts the game by drawing 5 cards from their deck. Each turn, a Player draws another card from the deck and chooses one to play.
        >>> test_card = Card('test', 100, 100)
        >>> test_deck = Deck([test_card.copy() for _ in range(6)])
        >>> test_player = Player(test_deck, 'tester')
        >>> len(test_deck.cards)
        1
        >>> len(test_player.hand)
        5
        """
        self.deck = deck
        self.name = name
        "*** YOUR CODE HERE ***"
        self.hand = []
        for i in range(5):
            self.hand.append(deck.draw())
        # self.hand = [deck.draw() for _ in range(5)]

    def draw(self):
        """Draw a card from the player's deck and add it to their hand.
        >>> test_card = Card('test', 100, 100)
        >>> test_deck = Deck([test_card.copy() for _ in range(6)])
        >>> test_player = Player(test_deck, 'tester')
        >>> test_player.draw()
        >>> len(test_deck.cards)
        0
        >>> len(test_player.hand)
        6
        """
        assert not self.deck.is_empty(), 'Deck is empty!'
        "*** YOUR CODE HERE ***"
        self.hand.append(deck.draw())

    def play(self, card_index):
        """Remove and return a card from the player's hand at the given index.
        >>> from cards import *
        >>> test_player = Player(standard_deck, 'tester')
        >>> ta1, ta2 = TACard("ta_1", 300, 400), TACard("ta_2", 500, 600)
        >>> tutor1, tutor2 = TutorCard("t1", 200, 500), TutorCard("t2", 600, 400)
        >>> test_player.hand = [ta1, ta2, tutor1, tutor2]
        >>> test_player.play(0) is ta1
        True
        >>> test_player.play(2) is tutor2
        True
        >>> len(test_player.hand)
        2
        """
        "*** YOUR CODE HERE ***"
        return self.hand.pop(card_index)
```
After you complete this problem, you'll be able to play a working version of the game! Type
```
python3 cardgame.py
```
to start a game of Magic: The Lambda-ing!

This version doesn't have the effects for different cards, yet - to get those working, try out the optional questions below.

# Optional Questions
For the following sections, do **not** overwrite any lines already provided in the code. Additionally, make sure to uncomment any calls to print once you have implemented each method. These are used to display information to the user, and changing them may cause you to fail tests that you would otherwise pass.

## Q5: Tutors: Flummox
To really make this card game interesting, our cards should have effects! We'll do this with the `effect` function for cards, which takes in the opponent card, the current player, and the opponent player.

Implement the `effect` method for Tutors, which causes the opponent to discard the first 3 cards in their hand and then draw 3 new cards. Assume there at least 3 cards in the opponent's hand and at least 3 cards in the opponent's deck.

Remember to uncomment the call to `print` once you're done!
```python
class TutorCard(Card):
    cardtype = 'Tutor'

    def effect(self, other_card, player, opponent):
        """
        Discard the first 3 cards in the opponent's hand and have
        them draw the same number of cards from their deck.
        >>> from cards import *
        >>> player1, player2 = Player(player_deck, 'p1'), Player(opponent_deck, 'p2')
        >>> other_card = Card('other', 500, 500)
        >>> tutor_test = TutorCard('Tutor', 500, 500)
        >>> initial_deck_length = len(player2.deck.cards)
        >>> tutor_test.effect(other_card, player1, player2)
        p2 discarded and re-drew 3 cards!
        >>> len(player2.hand)
        5
        >>> len(player2.deck.cards) == initial_deck_length - 3
        True
        """
        "*** YOUR CODE HERE ***"
        opponent.hand = opponent.hand[3:]
        for _ in range(3):
            opponent.draw()
        #Uncomment the line below when you've finished implementing this method!
        print('{} discarded and re-drew 3 cards!'.format(opponent.name))
```

## Q6: TAs: Shift
Let's add an effect for TAs now! Implement the `effect` method for TAs, which swaps the attack and defense of the opponent's card.
```python
class TACard(Card):
    cardtype = 'TA'

    def effect(self, other_card, player, opponent):
        """
        Swap the attack and defense of an opponent's card.
        >>> from cards import *
        >>> player1, player2 = Player(player_deck, 'p1'), Player(opponent_deck, 'p2')
        >>> other_card = Card('other', 300, 600)
        >>> ta_test = TACard('TA', 500, 500)
        >>> ta_test.effect(other_card, player1, player2)
        >>> other_card.attack
        600
        >>> other_card.defense
        300
        """
        "*** YOUR CODE HERE ***"
        temp = other_card.attack
        other_card.attack = other_card.defense
        other_card.defense = temp
        # other_card.attack, other_card.defense = other_card.defense, other_card.attack
```
## Q7: The Professor Arrives
A new challenger has appeared! Implement the `effect` method for the Professor, who adds the opponent card's attack and defense to all cards in the player's deck and then removes all cards in the opponent's deck that have the same attack or defense as the opponent's card.
Note: You might run into trouble when you mutate a list as you're iterating through it. Try iterating through a copy instead! You can use slicing to copy a list:
```
  >>> lst = [1, 2, 3, 4]
  >>> copy = lst[:]
  >>> copy
  [1, 2, 3, 4]
  >>> copy is lst
  False
```
```python
class ProfessorCard(Card):
    cardtype = 'Professor'

    def effect(self, other_card, player, opponent):
        """
        Adds the attack and defense of the opponent's card to
        all cards in the player's deck, then removes all cards
        in the opponent's deck that share an attack or defense
        stat with the opponent's card.
        >>> test_card = Card('card', 300, 300)
        >>> professor_test = ProfessorCard('Professor', 500, 500)
        >>> opponent_card = test_card.copy()
        >>> test_deck = Deck([test_card.copy() for _ in range(8)])
        >>> player1, player2 = Player(test_deck.copy(), 'p1'), Player(test_deck.copy(), 'p2')
        >>> professor_test.effect(opponent_card, player1, player2)
        3 cards were discarded from p2's deck!
        >>> [(card.attack, card.defense) for card in player1.deck.cards]
        [(600, 600), (600, 600), (600, 600)]
        >>> len(player2.deck.cards)
        0
        """
        orig_opponent_deck_length = len(opponent.deck.cards)
        "*** YOUR CODE HERE ***"
        for card in player.deck.cards:
            card.attack += other_card.attack
            card.defense += other_card.defense
        for card in opponent.deck.cards[:]:
            if card.attack == other_card.attack or card.defense == other_card.defense:
                opponent.deck.card.remove(card)
        discarded = orig_opponent_deck_length - len(opponent.deck.cards)
        if discarded:
            #Uncomment the line below when you've finished implementing this method!
            print('{} cards were discarded from {}\'s deck!'.format(discarded, opponent.name))
            return
```
After you complete this problem, we'll have a fully functional game of Magic: The Lambda-ing! This doesn't have to be the end, though - we encourage you to get creative with more card types, effects, and even adding more custom cards to your deck!
