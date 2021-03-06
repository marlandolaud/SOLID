# SOLID
SOLID Principles of Object Oriented Design Examples for learning

## S - The Single Responsibility Principle
Single responsibility is the concept of a Class doing one specific thing aka "responsibility" and not trying to do more than it should, which is also referred to as High Cohesion.

Classes often start out with Low Cohesion, but typically after several releases and different developers adding onto them, suddenly you'll notice that it became a **Monster** or **God** class as some call it. So the class should be refactored.

#### This class should not include email validation because that is not related with a person behavior
```csharp
class Person
{
    public string Name { get; set; }
    public string SurName { get; set; }
    public string Email { get; set; }
    
    public Person(string name, string surName, string email)
    {
        this.SurName = surName;
        this.Name = name;
        if (this.ValidateEmail(email))
        {
            this.Email = email;
        }
        else
        {
            throw new Exception("Invalid email!");
        }
    }
    private bool ValidateEmail(string email)
    {
        Regex re = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");
        return re.IsMatch(email);
    }

    private void Greet()
    {
        Console.WriteLine("Hi!");
    }
}
```
#### We can improve the class above by removing the responsibility of email validation from the Person class and creating a new Email class that will have that responsibility:
```csharp
class Person
{
    public string Name { get; set; }
    public string SurName { get; set; }
    public Email Email { get; set; }
    
    public Person(string name, string surName, string email)
    {
        this.SurName = surName;
        this.Name = name;
        this.Email = new Email(email);
    }       

    private void Greet()
    {
        Console.WriteLine("Hi!");
    }
}

class Email
{
    public string EmailAddress { get; set; }

    public Email(string email)
    {
        if (this.ValidateEmail(email))
        {
            this.EmailAddress = email;
        }
        else
        {
            throw new Exception("Invalid email!");
        }
    }

    private bool ValidateEmail(string email)
    {
        Regex re = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");
        return re.IsMatch(email);
    }
}
```

## O - The Open / Close Principle
The open/closed principle states "software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification". such an entity can allow its behavior to be extended without modifying its source code.

#### adding a new shape to this AreaCalculator will require altering the source code and is a violation of the OCP
```csharp
public class Square
{
    public double Height { get; set; }
}

public class Circle
{
    public double Radius { get; set; }
}

public class Triangle
{
    public double FirstSide { get; set; }
    public double SecondSide { get; set; }
    public double ThirdSide { get; set; }
}

public class AreaCalculator
{
    public double Area(object[] shapes)
    {
        double area = 0;

        foreach (var shape in shapes)
        {

            if (shape is Square)
            {
                Square square = (Square)shape;
                area += Math.Sqrt(square.Height);
            }

            if (shape is Triangle)
            {
                Triangle triangle = (Triangle)shape;
                double TotalHalf = (triangle.FirstSide + triangle.SecondSide + triangle.ThirdSide) / 2;
                area += Math.Sqrt(TotalHalf * (TotalHalf - triangle.FirstSide) *
                (TotalHalf - triangle.SecondSide) * (TotalHalf - triangle.ThirdSide));
            }

            if (shape is Circle)
            {
                Circle circle = (Circle)shape;
                area += circle.Radius * circle.Radius * Math.PI;
            }

        }
        return area;
    }
}
```
#### A better way to tackle the problem is allow the shapes to handle the implementation logic of calculating their own area
```csharp
public abstract class Shape
{
    public abstract double Area();
        
}

public class Square: Shape
{
    public double Height { get; set; }

    public override double Area()
    {
        return Math.Sqrt(Height);
    }
}

public class Circle: Shape
{
    public double Radius { get; set; }

    public override double Area()
    {
        return Radius * Radius * Math.PI;
    }
}

public class Triangle: Shape
{
    public double FirstSide { get; set; }
    public double SecondSide { get; set; }
    public double ThirdSide { get; set; }

    public override double Area()
    {
        double TotalHalf = (FirstSide + SecondSide + ThirdSide) / 2;
        return Math.Sqrt(TotalHalf * (TotalHalf - FirstSide) * (TotalHalf - SecondSide) * (TotalHalf - ThirdSide));
    }
}

```

## L - The Liskov Substitution Principle
If S is a subtype of T, then objects of type T may be replaced with objects of type S without altering any of the desirable properties of the program. objects of type T may be substituted with any object of a subtype S

Child class must not:
1) Remove base class behavior
2) Violate base class **invariants** (reasonable assumptions of behavior by clients)

**IS-A** should be substituted for **IS substitutable for**

Calling code should not know that there is a difference between derived class and a base type

#### LSP Tips:
**Tell, Don't Ask** : Don't Interrogate objects for their internals - move behavior to the object; tell the object what you want it to do.

**Conceder Refactoring to a new base class** given two classes that share a lot of behavior but are not substitutable

## I - The Interface Segregation principle
The interface-segregation principle (ISP) states that no client should be forced to depend on methods it does not use. ISP splits interfaces that are very large into smaller and more specific ones so that clients will only have to know about the methods that are of interest to them.

Avoid fat interfaces and forcing users to initialize code to execute one method

Refactor large interfaces so they inherit smaller interfaces

Keep interfaces lean and focused

#### Not The Best
```csharp
public interface IMessage
{
    IList<String> ToAddresses { get; set; }
    string MessageBody { get; set; }
    string Subject { get; set; }
    bool Send();
}

public class SmtpMessage:IMessage
{
    public IList<String> ToAddresses { get; set; }
    public string MessageBody { get; set; }
    public string Subject { get; set; }

    public bool Send()
    {
        //Do the real work here
        return true;
    }
}
```
#### Better
```csharp
public interface IMessage
{
    bool Send(IList<String> toAddresses, string messageBody);
}

public interface IEmailMessage:IMessage
{
    string Subject { get; set; }
    IList<String> BccAddresses { get; set; }
}

public class SmtpMessage : IEmailMessage
{
    public IList<String> BccAddresses { get; set; }
    public string Subject { get; set; }
    public bool Send(IList<String> toAddresses, string messageBody)
    {
        //Do the real work here
        return true;
    }
}

public class SmsMessage : IMessage
{
    public bool Send(IList<String> toAddresses, string messageBody)
    {
        //Do the real work here
        return true;
    }
}
```
The larger the interface, the more likely it is that you will need to change clients in the future.

## D - Dependency Inversion Principle

![alt text](https://lh3.googleusercontent.com/60NpYzzxYr4v60JZTR9c__o-7bABBoYI1ff9ancTw42FYlS6MKsWjjyXpMPlQ8g1LzTJ0uVKQMcKvRioCuswfkKMKFoWUKQg23zCQTZJ0-ruGQ1uMJNKDPv-gxV-W5ufHNTIG4ii)

### "Depend on abstraction instead of concrete types"

The dependency inversion principle refers to a specific form of decoupling software modules.

**A dependency is something that affects the behavior of the application** 
(Framework, Third-party library, Database, File system, Email, Web service, Sys resources, Config, The new keyword, Static methods, Thread.Sleep, Random)

#### Not the best
```csharp
public class Solver
{
  ITool tool;
  
  public Solver()
  {
    this.tool = new Hammer();    
  }
}
```

Three primary Techniques
#### Constructor Injection
```csharp
public class Solver
{
  ITool tool;
  
  public Solver(ITool tool)
  {
    this.tool = tool;
  }
  
  public void DoSomething()
  {
    tool.UseTool();
  }
}
```

#### Property Injection (using Ninject)
```csharp
public class Solver
{
  [Inject]
  public ITool tool { private get; set; }
  
  public void DoSomething()
  {
    tool.UseTool();
  }
}
```

#### Parameter Injection (using Ninject)
```csharp
public class Solver
{
  [Inject]
  public void DoSomething(ITool tool)
  {
    tool.UseTool();
  }
}
```

The new keyword can be a DIP smell if it is not a primitive type **"new is glue"**

Don’t force high-level modules to depend on low-level modules through direct instantiation or static method calls

Declare class dependencies explicitly in their constructor (best), property, or parameter injection
