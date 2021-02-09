## ENSE 370 - Software Systems Design - Laboratory

# Lab 3: Abstract Factory Design Pattern

### University of Regina
### Faculty of Engineering and Applied Science - Software Systems Engineering

### Lab Instructor: [Adam Tilson](mailto:Adam.Tilson@uregina.ca)

---

## Introduction

In this lab we will discuss the Abstract Factory design pattern. This is a creational design pattern which promotes dependency inversion. It allows our high level classes to break dependencies on the concrete implementations of low level classes when instantiating them. It accomplishes this in two ways: first, by delegating creation methods to Factories, objects which contain loosely coupled factory methods which create these objects; and second, by depending only on high level abstract classes rather than their concrete children. 

Dang that's a lot of confusing words - hopefully by demonstrating this pattern in action, these statements will become less confusing.

## Reminder: UML Relationships

In order to understand this pattern, it's useful to recall the meaning of the following arrows:

![UML Arrows][UML Arrows]

- `Association`: The class contains a reference to the class that is pointed to

- `Inheritance`: The class is a subclass of the class that is pointed to

- `Dependency`: If the class which is pointed to changes, the class which points to it must also change


## 1. Concrete Implementation using 'new' keyword

Consider a dungeon crawling game. In this game there are dungeons made up of three types of rooms, a Monster Room, a Treasure Room and a Boss Room. Each floor will consiste of a linear series of these rooms.

Using object oriented programming, each of these rooms will be a child of the `Room` class:

![Rooms Hierarchy][Rooms Hierarchy]

The main class is called `Game`, as shown in the following code snippet.

```java
import java.util.ArrayList;

public class Game {

    public ArrayList<Room> rooms = new ArrayList<Room>();
    public static void main (String [] args){
        Game game = new Game();
        game.rooms.add(new MonsterRoom());
        game.rooms.add(new MonsterRoom());
        game.rooms.add(new TreasureRoom());
        game.rooms.add(new MonsterRoom());
        game.rooms.add(new BossRoom());
        for (Room room : game.rooms) {
            room.display();
        }

    }
}
```

 See the `eg-1` files for an example of this configuration in code.

By inspecting the code, we can see that `Game` instantiates different concrete `Room` types using the `new` keyword. 

There are two problems with this code:

First, the code is tightly coupled - `Game` must explicitly use the low level classes.

Second, This is a type of dependency - if the concrete room types were to change, e.g. rooms being renamed or moved, we would need to update the `Game` class. This means a high level class, `Game`, depends on a low level class, as shown in the following UML diagram.

The following UML diagram shows the system at the current state:

![Game Rooms Dependency][Game Rooms Dependency]

We will attempt to solve these problems in the next two sections.

## 2. Factory Method

A Factory Method is a method which takes a generic datatype, like a String, and returns a specific object. We can invoke a factory method in place of a constructor to create a specific concrete object. An object with a Factory Method is called a Factory.

A UML diagram of this configuration is shown below...

![Simple Factory][Simple Factory]

With code in `eg-2`

The major changes are adding a RoomFactory class, as follows:

```java
import java.lang.IllegalArgumentException;

public class RoomFactory {

    public Room createRoom(String roomName){
        if (roomName == "Treasure Room") {
            return new TreasureRoom();
        } else if (roomName == "Monster Room") {
            return new MonsterRoom();
        } else if (roomName == "Boss Room") {
            return new BossRoom();
        } else {
            throw new IllegalArgumentException("No corresponding class for room name: " + roomName);
        }
    }
}
```
and the Game class is updated as follows:

```java
import java.util.ArrayList;

public class Game {
    public RoomFactory roomFactory;

    public Game (RoomFactory roomFactory){
        this.roomFactory = roomFactory;
    }

    public ArrayList<Room> rooms = new ArrayList<Room>();
    public static void main (String [] args){
        Game game = new Game(new RoomFactory());
        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Treasure Room"));
        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Boss Room"));
        for (Room room : game.rooms) {
            room.display();
        }

    }
}
```

This code somewhat insulates us from change.

## 3. Abstract Factory

Now, the game is updated to add dungeons with two floors. And the Monster, Treasure and Boss rooms for the second floor need to be different than the first one. 

To try to reduce dependencies, a common abstract class will be extracted for each type of room, as shown in the following example:

![Monster Room Abstract][Monster Room Abstract]

Where F1 means floor 1, F2 means floor 2, etc. This pattern will be repeated for each type of room.

This change is perfectly suited for use of the abstract factory pattern!

![Abstract Factory][Abstract Factory]

In this design pattern, we have both a hierarchy of Abstract Classes, and a hierarchy of Abstract Factories, and the `Game` uses the highest level abstraction in both cases. For our dungeon room factory, we would expect something like...

![Factory Hierarchy][Factory Hierarchy]

## Assignment

Your assignment is to document and complete the application for the second floor.

Part 1. Create the complete UML Class diagram for this problem. Including all concrete classes and factories, documented with appropriate dependencies. You may adapt the `.uxf` file included in the `eg-3` files

Part 2. Create Java files to implement the rest of the application, including required abstract classes and concrete classes for the second floor. Write some appropriate flavour text for the monsters and rewards for the second floor. The code should run with the following Game class, which is included in the `eg-3` files:

```java
import java.util.ArrayList;

public class Game {
    public RoomFactory roomFactory;
    public ArrayList<Room> rooms = new ArrayList<Room>();

    public Game (RoomFactory roomFactory){
        this.roomFactory = roomFactory;
    }

    public void setFactory (RoomFactory newFactory) {
        this.roomFactory = newFactory;
    }   

    public static void main (String [] args){

        Game game = new Game(new RoomFactoryF1());

        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Treasure Room"));
        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Boss Room"));

        game.setFactory(new RoomFactoryF2());

        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Treasure Room"));
        game.rooms.add(game.roomFactory.createRoom("Monster Room"));
        game.rooms.add(game.roomFactory.createRoom("Boss Room"));

        for (Room room : game.rooms) {
            room.display();
        }
    }
}
```
Part 3: Answer the following questions in a text document:
   
3.a) Picking up from where your code left off in part 2, If you were to add a new room type, which is available on all floors, which classes would need to be added? Which classes would need to change?
   
3.b) Picking up from where your code left off in part 2, If you were to add a new floor, which contains all of the existing room types, which classes would need to be added? Which classes would need to change?

You do not need to code the adjustments, just list them in the text file. 
   
## Submission

Please submit your assignment using the course submission utility on Snoopy. 

Please export your UML files as an image, e.g. `.png` or `.pdf`.

You may zip your UML file, source files and solutions together. 

Here is the command for submitting this assignment:
```
~ense370/bin/submit L03 your-file.zip
```
You may check that you have submitted your assignment correctly with the command:
```
~ense370/bin/submit --check
```

---

## References

E. Freeman, E. Robsen, B. Bates, & K. Sierra, Head First Design Patterns: A Brain-Friendly Guide, O'Reilly, 2004.

[UML Arrows]: res/uml-arrows.png "UML Arrows"
[Rooms Hierarchy]: res/rooms-hierarchy.png "Rooms Hierarchy"
[Game Rooms Dependency]: res/game-rooms-dependency.png "Game Rooms Dependency"
[Simple Factory]: res/simple-factory.png "Simple Factor"
[Monster Room Abstract]: res/monster-room-abstract.png "Monster Room Abstract"
[Abstract Factory]: res/abstract-factory.png "Abstract Factory"
[Factory Hierarchy]: res/factory-hierarchy.png "Factory Hierarchy"
