# SOLID 原则

原文链接：[🔗](https://mp.weixin.qq.com/s/L1NGv8hw4XW81e19VwMIuA)

## 1. Single Responsibility Principle (SRP)

**定义：** 一个类只有一个改变的原因，这意味着它应该只有一个工作或职责
**说明：** _SRP_ 旨在通过确保每个类处理单个功能来解耦代码。这使得代码更易于理解、测试和维护。

```csharp {.line-numbers}
// 这个类有两个职责：生成和打印发票
public class Invoice
{
    public void GenerateInvoice()
    {
        // Code to generate invoice
    }

    public void PrintInvoice()
    {
        // Code to print invoice
    }
}
```

```csharp {.line-numbers}
// 拆分成两个单独职责类：发票生成器 和 发票打印器
public class InvoiceGenerator
{
    public void GenerateInvoice()
    {
        // Code to generate invoice
    }
}

public class InvoicePrinter
{
    public void PrintInvoice()
    {
        // Code to print invoice
    }
}
```

## 2. Open/Closed Principle (OCP)

**定义：** 软件实体（类、模块、函数等）应该开放以供扩展，但要关闭以供修改。
**说明：** *OCP*  鼓励以一种允许在不修改现有代码的情况下扩展行为的方式进行软件设计。这可以通过抽象来实现，例如接口和抽象类。

```csharp {.line-numbers}
public class Rectangle
{
 public double Width { get; set; }
    public double Height { get; set; }

    public double Area()
    {
        return Width * Height;
    }
}

public class Circle
{
    public double Radius { get; set; }

    public double Area()
    {
        return Math.PI * Radius * Radius;
    }
}
```

```csharp {.line-numbers}
// 引入一个接口，如此可以在不修改现有类的情况下添加新形状
public interface IShape
{
    double Area();
}

public class Rectangle : IShape
{
    public double Width { get; set; }
    public double Height { get; set; }

    public double Area()
    {
        return Width * Height;
    }
}

public class Circle : IShape
{
    public double Radius { get; set; }

    public double Area()
    {
        return Math.PI * Radius * Radius;
    }
}
```

## 3. Liskov Substitution Principle (LSP)

**定义：** 子类型必须可以替换其基本类型，而不改变程序的正确性。
**说明：** _LSP_ 确保派生类在不更改其行为的情况下扩展基类。此原则对于维护可靠的继承层次结构至关重要。

```csharp {.line-numbers}
public class Bird
{
    public virtual void Fly()
    {
        // Fly
    }
}

public class Penguin : Bird
{
    // 违反了 LSP，因为企鹅无法飞行。更好的方法是创建一个单独的层次结构
    public override void Fly()
    {
        throw new NotSupportedException("Penguins can't fly.");
    }
}
```

```csharp {.line-numbers}
public abstract class Bird
{
    public abstract void Move();
}

public class FlyingBird : Bird
{
    public override void Move()
    {
        // Fly
    }
}

public class NonFlyingBird : Bird
{
    public override void Move()
    {
        // Walk
    }
}
```

## 4. Interface Segregation Principle (ISP)

**定义：** 不应强迫客户端依赖于他们不使用的接口。
**说明：** _ISP_ 主张创建小型的特定接口，而不是大型的通用接口。这样可以防止实现类被它们不需要的方法所累。

```csharp {.line-numbers}
public interface IWorker
{
    void Work();
    void Eat();
}

public class Worker : IWorker
{
    public void Work()
    {
        // Work
    }

    public void Eat()
    {
        // Eat
    }
}

public class Robot : IWorker
{
    public void Work()
    {
        // Work
    }

    public void Eat()
    {
        throw new NotImplementedException();
    }
}
```

```csharp {.line-numbers}
public interface IWorkable
{
    void Work();
}

public interface IFeedable
{
    void Eat();
}

public class Worker : IWorkable, IFeedable
{
    public void Work()
    {
        // Work
    }

    public void Eat()
    {
        // Eat
    }
}

public class Robot : IWorkable
{
    public void Work()
    {
        // Work
    }
}
```

## 5. Dependency Inversion Principle (DIP)

**定义：** 高级模块不应依赖于低级模块。两者都应该依赖于抽象。抽象不应该依赖于细节。细节应该取决于抽象。
**说明：** _DIP_ 旨在通过使用抽象（接口或抽象类）来解耦依赖关系，从而减少高级模块和低级模块之间的耦合。

```csharp {.line-numbers}
public class Light
{
    public void TurnOn()
    {
        // Turn on the light
    }

    public void TurnOff()
    {
        // Turn off the light
    }
}

public class Switch
{
    private readonly Light _light;

    public Switch(Light light)
    {
        _light = light;
    }

    public void Operate()
    {
        _light.TurnOn();
    }
}
```

```csharp {.line-numbers}
public interface ISwitchable
{
    void TurnOn();
    void TurnOff();
}

public class Light : ISwitchable
{
    public void TurnOn()
    {
        // Turn on the light
    }

    public void TurnOff()
    {
        // Turn off the light
    }
}

public class Switch
{
    private readonly ISwitchable _switchable;

    public Switch(ISwitchable switchable)
    {
        _switchable = switchable;
    }

    public void Operate()
    {
        _switchable.TurnOn();
    }
}
```
