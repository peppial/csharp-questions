# Mid-Level C# Quiz Questions

Test your intermediate C# knowledge with these carefully selected questions covering important concepts like async/await, LINQ, generics, inheritance, and more.

## Question 1: Array Reference vs Value

What's the output?

```csharp
class Program
{
    static void Main()
    {
        int[] numbers = [ 1, 2, 3 ];
        ModifyArray(numbers);

        Console.WriteLine(numbers == null ? "Array is null" : $"Array is not null, Length: {numbers.Length}");
    }

    static void ModifyArray(int[] arr)
    {
        arr = null;
    }
}
```

**Options:**
- A: Array is not null, Length: 3
- B: Array is not null, Length: 0
- C: Array is null
- D: Not all code paths return a value

[**Answer**](#answers)

---

## Question 2: Lambda Closure

What's the output?

```csharp
class Program
{
    static void Main()
    {
        List<Action> actions = new List<Action>();

        for (int i = 0; i < 3; i++)
        {
            Action a = () => Console.WriteLine($"Lambda captured: {i}");
            actions.Add(a);
        }

        foreach (var action in actions)
        {
            action();
        }
    }
}
```

**Options:**
- A: Lambda captured: 0 Lambda captured: 1 Lambda captured: 2
- B: Lambda captured: 3 Lambda captured: 3 Lambda captured: 3
- C: Lambda captured: 2 Lambda captured: 2 Lambda captured: 2
- D: Compiler error

[**Answer**](#answers)

---

## Question 3: Async/Await Exception Handling

What's the output?

```csharp
class Program
{
    static async Task Main()
    {
        try
        {
            var task = ThrowingMethodAsync();
            Console.WriteLine("Task started, waiting for it to complete...");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Caught: {ex.Message}");
        }
    }

    static async Task ThrowingMethodAsync()
    {
        await Task.Delay(1000);
        throw new InvalidOperationException("Something went wrong");
    }
}
```

**Options:**
- A: Task started, waiting for it to complete... Something went wrong
- B: Something went wrong
- C: Task started, waiting for it to complete...
- D: The application crashes

[**Answer**](#answers)

---

## Question 4: String Interning

What's the output?

```csharp
class Program
{
    static void Main()
    {
        string a = "hello";
        string b = "he" + "llo";
        string c = string.Concat("he", "llo");
        string d = new string("hello".ToCharArray());

        Console.WriteLine(object.ReferenceEquals(a, b));
        Console.WriteLine(object.ReferenceEquals(a, c));
        Console.WriteLine(object.ReferenceEquals(a, d));
    }
}
```

**Options:**
- A: True, False, False
- B: True, True, True
- C: False, False, False
- D: True, True, False

[**Answer**](#answers)

---

## Question 5: Static Field Initialization Order

What's the output?

```csharp
class Program
{
    private static int a = b;
    private static int b = 10;

    static void Main()
    {
        Console.WriteLine(a);
    }
}
```

**Options:**
- A: 10
- B: Use of unassigned local variable 'b'
- C: Runtime Error
- D: 0

[**Answer**](#answers)

---

## Question 6: Span and Memory

What's the output?

```csharp
class Program
{
    static void Main()
    {
        int[] numbers = [ 1, 2, 3, 4, 5 ];
        Span<int> slice = numbers.AsSpan(1, 3);

        for (int i = 0; i < slice.Length; i++)
        {
            slice[i] *= 2;
        }

        Console.WriteLine(string.Join(", ", numbers));
    }
}
```

**Options:**
- A: 2, 4, 6, 4, 5
- B: 1, 4, 6, 8, 5
- C: 1, 2, 3, 4, 5
- D: Compile-time error: cannot slice an array with Span

[**Answer**](#answers)

---

## Question 7: Virtual vs New Methods

What's the output?

```csharp
class Base
{
    public virtual void Show() => Console.WriteLine("Base.Show");
}

class Derived : Base
{
    public void Show() => Console.WriteLine("Derived.Show");
}

class Program
{
    static void Main()
    {
        Base obj = new Derived();
        obj.Show();
    }
}
```

**Options:**
- A: Runtime error
- B: Derived.Show
- C: No Output
- D: Base.Show

[**Answer**](#answers)

---

## Question 8: Boxing and Reference Equality

What's the output?

```csharp
struct S { }

class Program
{
    static void Main()
    {
        S s = new S();
        object o1 = s;
        object o2 = s;
        Console.WriteLine(object.ReferenceEquals(o1, o2));
    }
}
```

**Options:**
- A: True
- B: False
- C: Runtime exception
- D: Compiler error

[**Answer**](#answers)

---

## Question 9: LINQ Deferred Execution

What's the output?

```csharp
class Program
{
    static void Main()
    {
        var numbers = Enumerable.Range(1, 5);
        var query = numbers.Where(x => x % 2 == 0).Select(x => x * x);

        numbers = Enumerable.Range(10, 5);

        foreach (var n in query)
            Console.Write(n + " ");
    }
}
```

**Options:**
- A: 4 16
- B: 100 144
- C: Nothing (empty output)
- D: Throws an exception

[**Answer**](#answers)

---

## Question 10: Async void vs Task

What's the output?

```csharp
class Program
{
    static void Main()
    {
        SomethingAsync();
        Console.WriteLine("Hello World!");
    }

    static async void SomethingAsync()
    {
        await Task.Delay(1000);
        throw new Exception("Exception");
    }
}
```

**Options:**
- A: The application crashes
- B: Hello World!
- C: Compiler error
- D: Hello World! and the application crashes.

[**Answer**](#answers)

---

## Question 11: Record Equality

What's the output?

```csharp
record Person(string Name);
record Employee(string Name) : Person(Name);

class Program
{
    static void Main()
    {
        Person p1 = new Person("Alice");
        Person p2 = new Employee("Alice");

        Console.WriteLine(p1 == p2);
        Console.WriteLine(ReferenceEquals(p1, p2));
    }
}
```

**Options:**
- A: Compilation error
- B: True, True
- C: False, False
- D: True, False

[**Answer**](#answers)

---

## Question 12: Pattern Matching with Records

What's the output?

```csharp
record Shape;
record Circle(double Radius) : Shape;
record Rectangle(double Width, double Height) : Shape;

class Program
{
    static void Main()
    {
        Shape s = new Circle(5);

        string result = s switch
        {
            Rectangle { Width: > 0, Height: > 0 } => "Rectangle",
            Circle { Radius: > 10 } => "Large Circle",
            Circle => "Small Circle",
            _ => "Unknown"
        };

        Console.WriteLine(result);
    }
}
```

**Options:**
- A: Rectangle
- B: Large Circle
- C: Small Circle
- D: Unknown

[**Answer**](#answers)

---

## Question 13: Generic Static Fields

What's the output?

```csharp
Foo<int>.Count++;
Console.WriteLine(Foo<double>.Count);

class Foo<T>
{
    public static int Count;
}
```

**Options:**
- A: 0
- B: 1
- C: Compilation Error
- D: Runtime error

[**Answer**](#answers)

---

## Question 14: Exception Hierarchy

What's the output?

```csharp
try
{
    var array = new int[] { 1, 2 };
    Console.Write(array[5]);
}
catch(ApplicationException e)
{
    Console.Write(1);
}
catch(SystemException e)
{
    Console.Write(2);
}
catch(Exception e)
{
    Console.Write(3);
}
```

**Options:**
- A: 1
- B: 2
- C: 3
- D: Runtime error

[**Answer**](#answers)

---

## Question 15: Method Overload Resolution

What's the output?

```csharp
object o = "hello";
A.Print(o);

class A
{
    public static void Print<T>(T value) => Console.WriteLine("Generic");
    public static void Print(string value) => Console.WriteLine("String");
}
```

**Options:**
- A: Exception is thrown
- B: Compilation error
- C: Generic
- D: String

[**Answer**](#answers)

---

# Answers

## Answer 1: Array Reference vs Value

**A: Array is not null, Length: 3**

Arrays are reference types, but when you pass an array to a method, you're passing the reference by value. Setting `arr = null` inside the method only affects the local parameter, not the original reference.

[**Back**](#question-1-array-reference-vs-value)

## Answer 2: Lambda Closure

**B: Lambda captured: 3 Lambda captured: 3 Lambda captured: 3**

The lambda captures the variable `i` by reference, not by value. When the loop completes, `i` equals 3, so all lambdas output the same value.

[**Back**](#question-2-lambda-closure)

## Answer 3: Async/Await Exception Handling

**C: Task started, waiting for it to complete...**

The task is created but not awaited, so the exception remains unobserved. The try-catch only catches synchronous exceptions from the task creation, not from the task execution.

[**Back**](#question-3-asyncawait-exception-handling)

## Answer 4: String Interning

**D: True, True, False**

Compile-time string concatenation and `string.Concat` with literals are interned, but `new string()` creates a new instance on the heap.

[**Back**](#question-4-string-interning)

## Answer 5: Static Field Initialization Order

**D: 0**

Static fields are initialized in declaration order. When `a = b` is evaluated, `b` hasn't been initialized yet, so it has its default value of 0.

[**Back**](#question-5-static-field-initialization-order)

## Answer 6: Span and Memory

**B: 1, 4, 6, 8, 5**

`Span<T>` provides a view over the original array. Changes through the span affect the underlying array. The slice starts at index 1 with length 3.

[**Back**](#question-6-span-and-memory)

## Answer 7: Virtual vs New Methods

**D: Base.Show**

`Derived.Show()` hides the base method but doesn't override it (missing `override` keyword). Since the reference is `Base`, the base method is called.

[**Back**](#question-7-virtual-vs-new-methods)

## Answer 8: Boxing and Reference Equality

**B: False**

Each assignment to `object` boxes the struct into a new object on the heap. `o1` and `o2` are different object instances.

[**Back**](#question-8-boxing-and-reference-equality)

## Answer 9: LINQ Deferred Execution

**A: 4 16**

LINQ queries are executed when enumerated, not when defined. The query captures the original `numbers` reference (1-5), not the new one (10-14).

[**Back**](#question-9-linq-deferred-execution)

## Answer 10: Async void vs Task

**B: Hello World!**

`async void` methods are fire-and-forget. The exception is thrown on a different thread context and doesn't crash the main thread.

[**Back**](#question-10-async-void-vs-task)

## Answer 11: Record Equality

**C: False, False**

Records of different types are never equal, even if they have the same property values. They're also different object instances.

[**Back**](#question-11-record-equality)

## Answer 12: Pattern Matching with Records

**C: Small Circle**

The Circle with Radius 5 doesn't match the "Large Circle" pattern (Radius > 10), so it matches the general `Circle` pattern.

[**Back**](#question-12-pattern-matching-with-records)

## Answer 13: Generic Static Fields

**A: 0**

Each generic type instantiation has its own copy of static fields. `Foo<int>.Count` and `Foo<double>.Count` are separate fields.

[**Back**](#question-13-generic-static-fields)

## Answer 14: Exception Hierarchy

**B: 2**

`IndexOutOfRangeException` inherits from `SystemException`, which is caught by the second catch block.

[**Back**](#question-14-exception-hierarchy)

## Answer 15: Method Overload Resolution

**C: Generic**

The compile-time type of `o` is `object`, so the generic method `Print<T>(T value)` is selected over the specific `Print(string)` overload.

[**Back**](#question-15-method-overload-resolution)

