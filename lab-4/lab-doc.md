## ENSE 370 - Software Systems Design - Laboratory

# Lab 4: Decorator Design Pattern

### University of Regina
### Faculty of Engineering and Applied Science - Software Systems Engineering

### Lab Instructor: [Adam Tilson](mailto:Adam.Tilson@uregina.ca)

---

### Introduction

This lab will introduce the decorator design pattern.

The decorator is a structural design pattern allows you to...
- add behaviours to an object at runtime
- provide greater flexibilty than inheritance
- simplify your code by greatly reducing the number of classes
  
Reminder: important arrows:

![UML Arrows][UML Arrows]

### Example: Role Playing Game Character

Consider a role playing game in which a Knight character has the ability to equip different weapons. Equipping different weapons will change
- The description that is printed when the character attacks
- The amount of damage that the attack does

Let's try to use inheritance to create this system:

![Knight One Weapon][Knight One Weapon]

This could be implemented with the following code, which is included in the `eg-1` files...


```java
public abstract class Knight {

    public void getAttackDescription(){
        System.out.print("The knight attacks with: ");
    }
    
    public int getAttackDamage(){
        return 0;
    }
}
```

and

```java
public class KnightWithSword extends Knight {

    @Override
    public void getAttackDescription(){
        super.getAttackDescription();
        System.out.print("Sword. ");
    }

    @Override
    public int getAttackDamage(){
        return 10;
    }
}
```

With similar code for the other classes.

We will test these classes through the Game class...

```java
public class Game {

    public static void main (String [] args) {

        int damage;
        Knight firstKnight = new KnightWithBow();
        firstKnight.getAttackDescription();
        damage = firstKnight.getAttackDamage();
        System.out.println(" The attack does " + damage + " damage.");

        Knight secondKnight = new KnightWithSword();
        secondKnight.getAttackDescription();
        damage = secondKnight.getAttackDamage();
        System.out.println(" The attack does " + damage + " damage.");

        Knight thirdKnight = new KnightWithDagger();
        thirdKnight.getAttackDescription();
        damage = thirdKnight.getAttackDamage();
        System.out.println(" The attack does " + damage + " damage.");
    }
}
```
### Example 2: Mulitple Weapons

Now, consider an adjustment to the game as follows:

- The character has the ability to equip multiple weapons, and attack multiple times.
- This will print all of the equipped weapons in the attack descrption.
- It will also sum the damages of each weapon together.

We can use the ability of classes to invoke their parents class to easily achieve this through inheritance. Consider the branch of a Knight with a sword and a bow...

![Knight Two Weapons][Knight Two Weapons]

Which would lead to the following three classes... (eg-2)

```java
public abstract class Knight {

    public void getAttackDescription(){
        System.out.print("The knight attacks with: ");
    }
    
    public int getAttackDamage(){
        return 0;
    }
}
```

and

```java
public class KnightWithSword extends Knight {

    @Override
    public void getAttackDescription(){
        super.getAttackDescription();
        System.out.print("Sword. ");
    }

    @Override
    public int getAttackDamage(){
        return 10;
    }
}
```

and

```java
public class KnightWithSwordAndBow extends KnightWithSword {

    @Override
    public void getAttackDescription(){
        super.getAttackDescription();
        System.out.print("Bow. ");
    }

    @Override
    public int getAttackDamage(){
        // 10 for sword + 5 for bow
        return 15;
    }
}
```

And we can test this with the game class...

```java
public class Game {

    public static void main (String [] args) {

        int damage;
        Knight firstKnight = new KnightWithSword();
        firstKnight.getAttackDescription();
        damage = firstKnight.getAttackDamage();
        System.out.println(" The attack does " + damage + " damage.");

        Knight secondKnight = new KnightWithSwordAndBow();
        secondKnight.getAttackDescription();
        damage = secondKnight.getAttackDamage();
        System.out.println(" The attack does " + damage + " damage.");

    }
}   
```

At first glance, this appears to be reasonable. But... what if we expand out the rest of the hierarchy...

![Class Explosion][Class Explosion]

Hopefully you can see some problems. There are a tonne of classes here. Waaay too many. 

Additionally, this code is not very flexible. 

What if, from gameplay testing, we discover that swords are too powerful and need to have their damage reduced (nerfed) for balance? We would have manually fix all of the classes that use a sword.

What if we needed to add a new weapon weapon? How many new classes would we need? 

What if we wanted to a new type of equipment, like shields or armor. We would have way too many classes.

Finally, what if equipment had magical effects, like the ability to cast spells. How would we check for this in our classes?

Our code has many problems... Can design patterns save us? 

### The Decorator Pattern

The decorator pattern essentially allows us to create child classes which are component-wise combinations of other classes.

![Decorator][Decorator]

In this case, our Knight is our component, and the decorations are the inventory items.

Note the composition relationship - the decorator class is composed of a number of components. But it also inherits from component, meaning that it may be composed of a number of decorators. This is kind of like "recursive inheritance". The concrete component has no references to other components, so it like the "base case".

One example of a possible object configuration created at runtime is...

![Possible Configuration][Possible Configuration]

Please Note: This class diagram is just one possible object configuration generated at runtime, and should not be part of the overall class diagram for the problem.

When the game wishes to get the attack damage for the knight, it will call getAttackDamage() on firstKnight, which is a BowDecorator, which will call it on knight, which is a Sword Decorator, which will call it on knight, which is a Knight (Base Case). The values returned can then be summed to provide the total damage. A similar strategy can be attempted for descriptions, but instead concatenating strings.

---

## Assignment

Your assignment is to document and complete the application using the decorator pattern such that the Knight can equip any combination of the three weapons we've looked at so far, and add three new weapons of your own.

Part 1. Create the complete UML Class diagram for this problem. Including all concrete classes. You may adapt the `.uxf` file included in the `eg-3` files

Part 2. Create Java files to implement the rest of the application, including required abstract classes and concrete classes, for all six weapons. Write some appropriate descriptions. The code should run with the following Game class, which is included in the `eg-3` files:

```java
public class Game {

    public static void main (String [] args) {

        int damage;
        AbstractKnight firstKnight = new BowDecorator ( new SwordDecorator ( new Knight()));
        firstKnight.getAttackDescription();
        damage = firstKnight.getAttackDamage();
        System.out.println(" The attack does " + damage + " damage.");

    }
}
```

Which should produce the following output:
```
The knight attacks with: Sword. Bow.  The attack does 15 damage.
```

## Submission

Please submit your assignment using the course submission utility on Snoopy. 

Please export your UML files as an image, e.g. `.png` or `.pdf`.

You may zip your UML file, source files and solutions together. 

Here is the command for submitting this assignment:
```
~ense370/bin/submit L04 your-file.zip
```
You may check that you have submitted your assignment correctly with the command:
```
~ense370/bin/submit --check
```

---

## References

E. Freeman, E. Robsen, B. Bates, & K. Sierra, Head First Design Patterns: A Brain-Friendly Guide, O'Reilly, 2004.

D. Banas, [Decorator Design Patterns](https://www.youtube.com/watch?v=j40kRwSm4VE), newthinktank, 2012

[UML Arrows]: res/arrows.png "UML Arrows"
[Knight One Weapon]: res/knight-one-weapon.png "Knight Hierarchy One Weapon"
[Knight Two Weapons]: res/knight-two-weapons-isolated.png "Knight with two weapons single branch"
[Class Explosion]: res/class-explosion.png "Full knight with two weapons hierarchy... oof"
[Decorator]: res/decorator.png "The decorator design pattern"
[Possible Configuration]: res/possible-configuration.png "One possible knight configuration using the decorator"

Adam's Notes:
// It's composition, because, if it is in use by the one player, it can't be used by another player.
