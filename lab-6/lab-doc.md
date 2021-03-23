## ENSE 370 - Software Systems Design - Laboratory

# Lab 6: Redesign and Refactoring

### University of Regina
### Faculty of Engineering and Applied Science - Software Systems Engineering

### Lab Instructor: [Adam Tilson](mailto:Adam.Tilson@uregina.ca)

---

## Introduction

In this lab we will look at an application for creating a coffee machine with an ASCII-ART UI which shows the size of the drink, the beverage, and the number of sugars and creams. It also generated coffeeCodes, a legacy ASCII interface for dispensing coffee. We will start from a rather poor but functional implementation, and move towards creating something with a better design and more open to expansion. We will look at it from the perspective of function refactoring, class-level refactoring and system refactoring.


## A quick tour through the code

- The main function asks the user a number of questions:
    - What size beverage?
        - After this, display an empty cup
    - Which beverage?
        - After this, fill the cup with the first letter of the beverage
    - How many sugars and creams?
        - Add a `$` icon for each sugar added, replacing a beverage letter
        - Add a `@` icon for each cream added, replacing a beverage letter
    - Print out the cost of the beverage according to the following schedule:
    - send the coffee code to the dispenser to create the correct coffee.
  
 size   | coffee | tea  | decaf  
 ------ | ------ | ---- | -----
 small  | 1.90   | 2.00 | 2.10 
 medium | 2.40   | 2.50 | 2.60 
 large  | 3.00   | 3.10 | 3.20

Sugar: Add 10 cents for each

Cream: Add 15 cents for each

The monolithic printCoffee function prints out the cup of coffee, and, if the user is done making their adjustments, the price.

Finally, the sendCoffeeCode prints out the Ascii-String Code for this coffee. 

A few helper functions assist with gathering inputs.

Some problems and changes we could make. 
(Intentionally Incomplete - Please add some as you go!)

## Note on Refactoring

- In refactoring, we wish to improve our code without changing the result. For this application, the systems to be preserved are:
  1. The coffee codes, as these interact with a legacy interface
  2. The prices
  3. The ASCII Art beverage display
- We can ensure that, as we change our code, we do not break anything, using testing principles. Check out the /test/JavaMakerTest.java code for some preliminary tests.
  
## Code Smells and Anti-Patterns

- This code is a bit funky. Can you find an example of...

<details>
<summary>Comments explaining unnecessary complex code</summary>
Basically everywhere....
</details>


<details>
<summary>Long Function</summary>

main is ~100 lines...
printCoffee is ~100 lines...

</details>

<details>
<summary> Long Parameter List </summary>
The print function takes quite a few... could some become class variables?
</details>

<details>
<summary> Inconsistent Names </summary>
The beverage is referred to as `drink` in the main, but `base` in the print functions.

</details>


<details>
<summary>Dead code</summary>

```java
        // Testing functions.
        //javaMaker.printCoffee ("Medium", "$$", "Coffee", false);
        //javaMaker.printCoffee ("Small", "@@", "Tea", false);
        //javaMaker.printCoffee ("Large", "$$@@", "Decaf", true);
```

</details>

<details>
<summary> Oddball Solution </summary>
Honestly the way the coffee cup is printed is bizarre, but I feel it makes sense and works.

</details>

<details>
<summary> Functions with too much responsibility </summary>
Printing the coffee should not also compute the price!
</details>


## Function-Level Refactors
<details>
<summary>Remove magic numbers</summary>
In the sendCoffeeCodes function, magic numbers are used to represent the number of symbols in each size cup.
</details>


<details>
<summary>Extract Function</summary>
In the sendCoffeeCodes function, we have some repeated code which can be turned into a function
</details>

## Class-Level Refactors

<details>
<summary>Extract Class Variable</summary>
All of the properties of the drink could be moved into class variables
</details>

<details>
<summary>Extract Class</summary>
Many things could be classes,  money, drinks, ingredients, display, input handling
</details>

## System-Level Refactors / Redesign
 
<details>
<summary>Redesign to Design Patterns</summary>
I can spot at least 3 which could be used, but possibly more too!
</details>

## The six refactors we'll do in the pre-lab (maybe?)
1. Remove magic numbers from sendCoffeeCodes
2. Change conditional logic to HashMap lookups
3. Decouple the print interface
4. Add more tests
5. Add class variables

The included code, assn-starter, should pick up after these refactors are complete!

## Assignment

Please perform the following refactors:
1. At least one system level redesign.
    - Apply a design pattern to the application, which should both simplify and allow extending the functionality of the application
   - Include a UML class diagram to document your pattern
2. A class level redesign
   - Extract at least 3 classes for the refactor
   - Try to obey the "single responsibility" principle.
   - These classes should also be in the UML Class Diagram
3. Function level refactor
   - Find at least 10 small refactors
   - Briefly describe the changes you made in a readme file - list the problem as a code smell, and your solution
  
- Your code should not change anything on the front end, e.g. how the cup is displayed, the price or the coffee codes. It can change anything internal, including interfaces, but be sure to update tests to use the new interfaces!
   
## Submission

Please submit your .java source and any UML files in .png or .pdf.

---

## References

Overview

M. Fowler, [Refactoring Catalog](https://refactoring.com/catalog/), ThoughtWorks

J. Atwood, [Code Smells](https://blog.codinghorror.com/code-smells/), Coding Horror


If you use a readme.md, and wish to do diffs, you can implement them lile this:

I wish I had learned about this sooner!

```diff
public class Hello
{
   public static void main(String [] args)
   {
-      System.out.println("Hello World");
+      System.out.println("Hello Java World!");
   }
}
```