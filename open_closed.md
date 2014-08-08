Open-Closed Principle
=====================

The foundation of the Open-Closed Principle is that software should be
*open* for extension, yet *closed* for modification. For a software
entity to be open for extension, it means that if the requirements for
the system change, that the program should be able to accomodate the new
requirements. It should be able to be *extended* to included the new
functionality. However, on the other side, we also want to *close* our
software to modification, meaning that we want to craft it in
such a way that no one should be able to (or will need to) change our
source code when adding in new requirments. We always want to **add** to
our code base, rather than changing what is already there. But, how do
we add behavior to our software
without modifying it? The key to be adhering to two concepts that are
seemingly at odds
with one another is abstraction.

We can begin by taking a look at an example of a piece of software that
breaks the Open-Closed Principle. Let's look at a ```Player``` module
that is part of a Tic Tac Toe game:
```
defmodule Player do
  def get_move(player, current_board) do
    cond do
      player.type == :human ->
        get_human_move
      player.type == :smart ->
        get_unbeatable_computer_player_move
      player.type == :dumb ->
        get_easy_computer_player_move
    end
  end
end
```
The ```Game``` module could look something like this:
```
defmodule Game do
  def game_loop do
    Player.get_move(smart_computer_player, current_board)
  end
end
```

As we can see, in the ```get_move``` method, we have a conditional that
checks what type of player is passed into the method, and then calls the
appropriate method depending on that type. This approach does seem to
work, but if we wanted to add new player types, our ```get_move```
method
would need to change each time to accomodate the new players. We would
need to change the source code, by adding conditions to our ```cond
do```, thus breaking the *closed* part of our Open-Closed
Principle. So, keeping this in mind, how are we able to refactor to
possibly allow for new types of players?

Let's start with creating an abstract idea of a *Player*. We could
then create derivative *Player* instances that implement the *Player*
interface, which the rest of the system is expecting. The way the system
is now, we would need a derivative player object for a human player, one
for a smart computer, one for a dumb computer, etc..

First, we can split our ```Player``` module into a ```HumanPlayer``` and
a ```SmartComputerPlayer``` and ```DumbComputerPlayer```. Not only will
this start us off in the right direction to adhering to Open-Closed
Principle, it will consequently help us with adhering to the Single
Responsibility Principle as well! Now, each player module is
responsibile for that type of player only, not *all* the players types.
But, back to Open-Closed Principle...

```
defmodule HumanPlayer do
  def get_move do
    #logic for getting a human player's move
  end
end
```
```
defmodule SmartComputerPlayer do
  def get_move do
    #logic for getting a smart computer's move
  end
end
```
```
defmodule SmartComputerPlayer do
  def get_move do
    #logic for getting a dumb computer's move
  end
end
```
Notice that ```HumanPlayer```, ```SmartComputerPlayer``` and
```DumbComputerPlayer``` all have a ```get_move``` method. So, in our
```Game``` module, when we call ```Player.get_move```, we would now be
able to call it on any type of *player*:

```
defmodule Game do
  def game_loop(player) do
    player.get_move(smart_computer_player, current_board)
  end
end
```
So, in order to add new and different player types, it would be as
simple as creating a new module that implements the *player* interface:
```
defmodule MediumComputerPlayer do
  def get_move do
    #logic for getting a medium difficulty computer's move
  end
end
```

An instance of our new ```MediumComputerPlayer``` module could then be
passed into our system when we start a new game, and since all player
modules have a ```get_move``` method, the system wouldn't know or care
what type of player it is currently dealing with. So, this software is
now *open* for extension in that we can easily add new player types.
Yet, it is *closed* to modifcation in that we are able to add
functionality (new player types) without needing to directly touch the
exiting player modules.
