# SOLID
SOLID Principles of Object Oriented Design Examples for learning

## S - The Single Responsibility Principle
## O - The Open / Close Principle
## L - The Liskov Substitution Principle

if S is a subtype of T, then objects of type T may be replaced with objects of type S without altering any of the desirable properties of the program 
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

tell dont ask

concider refactor to a base class if 

## I - the Interface Segregation principle
## D - Dependency Inversion Principle

