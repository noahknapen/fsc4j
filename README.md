# Formal Specifications Checker for Java

A modified version of [the Eclipse IDE](https://www.eclipse.org/downloads/packages/release/2021-12/r)'s Java Development Tools component that performs syntax checking, typechecking, and run-time checking of preconditions and postconditions specified as part of the `@pre` and `@post` tags in the Javadoc comments for methods and constructors, and class invariants specified as part of the `@invar` tags in the Javadoc comments for classes and fields. (Note: run-time checking of class invariants is currently incomplete; currently, only the invariants of `this` are checked.)

The current version also checks grammatical well-formedness and well-typedness of the formal parts of `@throws`, `@may_throw`, `@inspects`, `@mutates`, and `@mutates_properties` clauses, and grammatical well-formedness of the formal parts of `@creates` clauses. It does not yet check these clauses at run time.

## Example

```java
class GameCharacter {

    private int health;
    
    public int getHealth() { return health; }
    
    /**
     * @pre The specified initial health is nonnegative.
     *    | 0 <= initialHealth
     * @post This game character's health equals the specified initial health.
     *    | getHealth() == initialHealth
     */
    public GameCharacter(int initialHealth) {
        health = initialHealth;
    }
    
    /**
     * @pre The specified amount of health is nonnegative
     *    | 0 <= amount
     * @post This game character's health equals its old health minus the specified amount of health.
     *    | getHealth() == old(getHealth()) - amount
     */
    public void takeDamage(int amount) {
        health -= amount;
    }
}

class Main {

    public static void main(String[] args) {
        new GameCharacter(100).takeDamage(50);
    }
}
```

## Features
- Indicates compilation errors as you type. Compilation errors include syntax errors and type errors, as well as references inside Javadoc comments to elements that are not visible to all clients (such as references to private fields in the Javadoc for a public method) and side-effecting constructs (in particular: assignments) inside Javadoc comments.
- When renaming a program element referenced from a Javadoc comment using the Eclipse Rename refactoring, the Javadoc comment is updated appropriately.
- Run-time checks are performed only if assertions are enabled. Specify `-ea` on the JVM command line to enable assertions.

## Installation instructions

FSC4J is a modified version of the Java Development Tools component of [the Eclipse 2021-12 IDE](https://www.eclipse.org/downloads/packages/release/2021-12/r). First install Eclipse IDE for Java Developers 2021-12 (*FSC4J is not compatible with older or newer releases of Eclipse!*); then, in Eclipse's Help menu, choose *Install New Software...*. Then choose *Add...* to add the FSC4J software site:
- Name: `FSC4J`
- Location: `https://people.cs.kuleuven.be/~bart.jacobs/fsc4j`

Then, below *Uncategorized*, check *Eclipse Java Development Tools*. You can tell from the version number that this is an FSC4J version of this component. Then click *Next* and follow the on-screen instructions.

After restarting Eclipse, the FSC4J functionality should be active. Enter the following code; notice that `argss` is underlined:
```java
class Fsc4jTest {
    /** @pre | argss == null */
    public static void main(String[] args) {
    }
}
```
Now, replace `argss` by `args` and run the program; you will get an `AssertionError`. (If you do not get an error, open the Run Configuration and add `-ea` to the VM arguments so that assertions are enabled.)

## FAQ

### When installing, I get a "Conflicting Dependency: Eclipse DSL Tools" error

FSC4J conflicts with the Eclipse DSL Tools, which are included with the Eclipse IDE for Java and DSL Developers, but not with the Eclipse IDE for Java Developers. Solution: uninstall the Eclipse DSL Tools in About Eclipse -> Installation Details -> Installed Software -> Eclipse DSL Tools -> Uninstall (or install the Eclipse IDE for Java Developers).

### I'm not seeing any run-time checking behavior!

Make sure assertions are enabled. Specify `-ea` on the JVM command line. In Eclipse, open your run configuration and add `-ea` under *VM arguments*.

Also check that FSC4J was installed successfully. In About Eclipse (in Eclipse's Help menu on Windows; in the Eclipse menu on macOS) -> Installation Details -> Installed Software, check that the version number of Eclipse Java Development Tools mentions `fsc4j`.

### When stepping through the run-time checks in the Eclipse debugger, all kinds of platform classes are shown

Filter those out by enabling Step Filtering. Right-click on the offending stack frame in the Threads view and choose `Edit Step Filters...`. Enable `java.*`, `jdk.*`, `sun.*`.

### When installing, I get "Updates are not permitted"

Make sure your user account has write access to the folder where you installed Eclipse.

## TODO
- Perform typechecking of `@creates` clauses
- Perform run-time checking of `@throws`, `@may_throw`, `@inspects`, `@mutates`, `@mutates_properties`, and `@creates` clauses
- Perform more complete run-time checking of `@invar` clauses. (Currently, only the invariants of `this` are checked.)
- CodeAssist (autocompletion) inside Javadoc
