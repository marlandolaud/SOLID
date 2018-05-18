# SOLID
SOLID Principles of Object Oriented Design Examples for learning

## S - The Single Responsibility Principle
The single responsibility principle is a computer programming principle that states that every module or class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class.

## O - The Open / Close Principle
The open/closed principle states "software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification"; that is, such an entity can allow its behavior to be extended without modifying its source code.

## L - The Liskov Substitution Principle

If S is a subtype of T, then objects of type T may be replaced with objects of type S without altering any of the desirable properties of the program 
```
public class S
{
  public int DoSomething() => 1;
}

public class T : S
{
  public int DoSomething() => 2;
}

public class Reader
{
  public void Caller(S input) // objects of type T may be substituted with any object of a subtype S
  {
    //logic goes here
  }
}
```

Calling code should not know that there is a difference between derived class and a base type

Child class must not violate base class reasonable assumptions of behavior by clients also known as invariants

Tell don’t ask

Consider refactor to a base class if 

## I - the Interface Segregation principle
The interface-segregation principle (ISP) states that no client should be forced to depend on methods it does not use. ISP splits interfaces that are very large into smaller and more specific ones so that clients will only have to know about the methods that are of interest to them.

Avoid fat interfaces and forcing users to initialize code to execute one method

Refactor large interfaces so they inherit smaller interfaces

Keep interfaces lean and focused

## D - Dependency Inversion Principle
### "Don’t call us well call you"
### "Depend on abstraction instead of concrete types"

The dependency inversion principle refers to a specific form of decoupling software modules.

#### A dependency is something that affects the behavior of the application
Framework
Third-party library
Database
File system
Email
Web service
Sys resources
Config
The new keyword
Static methods
Thread. Sleep
Random

Three primary Techniques
#### Constructor Injection
```
public class Solver
{
  ITool tool;
  
  public Solver(ITool tool)
  {
    this.tool = tool;
  }
}
```

#### Property Injection
```
public class Solver
{
  //Ninject example
  [Inject]
  public ITool tool { private get; set; }
}
```

#### Parameter Injection
```
public class Solver
{
  public Solver()  {  }
  
  public int Hack(ITool tool)
  {
    return tool.Hack();
  }
}
```

The new keyword can be a DIP smell if it is not a primitive type

Don’t force high-level modules to depend on low-level modules through direct instantiation or static method calls

Declare class dependencies explicitly in their constructor (best), property, or parameter injection
