<div align="center">
  <h1>C# Questions</h1>
</div>

<div align="center">
From fundamentals to advanced, this C# quiz can test your knowledge, refresh your skills, or help prepare for a coding interview! 🚀

Answers are provided in collapsible sections below each question — just click to expand. Have fun, and good luck!

Any comments and suggestions are more than welcome.
</div>

---

<p><div align="center">This repo is created similary to Lydia Hallie famous repo
  <a href="https://github.com/lydiahallie/javascript-questions">javascript-questions</a>.</div><div align="center">Credits to her for the great idea and hard work! :heart: 
</div>

</p>


---

###### 1. What's the output?

```csharp
class Program
{
    static void Main()
    {
        Console.WriteLine(Test(null));
    }

    static string Test(object o) => "object";
    static string Test(dynamic d) => "dynamic";
}
```

- A: dynamic
- B: object
- C: Type 'Program' already defines a member called 'Test' with the same parameter types
- D: Cannot convert null literal to non-nullable reference type

<details><summary><b>Answer</b></summary>
<p>

#### Answer: C
In C#, method overloading is based on compile-time types. While dynamic behaves differently at runtime, at compile-time, dynamic is just an <b>alias</b> for object.
Since method overloading does not allow two methods with identical signatures (same method name and parameter types), the  <b>compiler does not allow this code to compile</b>.

</p>
</details>

---
###### 2. What's the output?

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

- A: Array is not null, Length: 3
- B: Array is not null, Length: 0
- C: Array is null
- D: Not all code paths return a value

<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
In C#, method parameters are passed by value by default. 
For reference types (like classes and arrays), the reference itself is passed by value - meaning <b>the method receives a copy of the reference</b>, not the original reference. However, both references point to the same object in memory, so changes to the object's properties within the method will be visible outside the method.

When you call ModifyArray(numbers); the value of the reference (a pointer to the array in memory) is copied into the method parameter arr.
So now, inside the method: <b>arr is a copy of the reference</b> to the original array numbers.
If you do arr = null;, you're only modifying the copy, not the original. The original numbers in Main() remains untouched.

</p>
</details>

---
###### 3. What's the output?

```csharp
class Program
{
    static object _lock = new object();

    static async Task Main()
    {
        DoWorkAsync();
    }
    
    static async Task DoWorkAsync()
    {
        lock (_lock)
        {
            Console.WriteLine("Entered lock");
            await Task.Delay(1000); 
            Console.WriteLine("Leaving lock");
        }
    }
}
```

- A: Entered lock, Leaving lock
- B: A deadlock
- C: No output
- D: Cannot await in the body of a lock statement

<details><summary><b>Answer</b></summary>
<p>

#### Answer: D
When you await inside a lock, you risk not releasing the lock immediately.
When the method resumes, it might do so on a <b>different thread</b> that didn’t acquire the lock originally, violating the Monitor’s rules.
This is why the compiler doesn't even let you do it. It throws an error to protect you from unsafe behaivour.

Instead, you should use <b>SemaphoreSlim</b>.
</p>
</details>

---
###### 4. What's the output?

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

- A: Lambda captured: 0
     Lambda captured: 1
     Lambda captured: 2
- B: Lambda captured: 3
     Lambda captured: 3
     Lambda captured: 3
- C: Lambda captured: 2
     Lambda captured: 2
     Lambda captured: 2
- D: Compiler error

<details><summary><b>Answer</b></summary>
<p>

#### Answer: B
Each lambda is capturing the variable i, not its value. The variable i is declared outside the lambda, and the lambda holds a reference to it.
When the foreach runs the lambdas, the loop has already finished — and i == 3. So, the lambdas don’t capture 0, 1, 2 — they all reference the same variable, and its final value is 3.

To fix it introduce a variable in the loop:

```csharp
for (int i = 0; i < 3; i++)
{
    int copy = i; // 👈 new variable per iteration
    actions.Add(() => Console.WriteLine($"Lambda captured: {copy}"));
}
```

</p>
</details>


---
###### 5. What's the output?

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

- A: The application crashes
- B: Hello World!
- C: Compiler error
- D: Hello World! and the application crashes.

<details><summary><b>Answer</b></summary>
<p>

#### Answer: B
When you call an async void method, it behaves like fire-and-forget. Control immediately returns to the caller — in this case, back to Main() — without waiting for the await to finish. Meanwhile, the await Task.Delay(1000) delays execution for 1 second. 
After that delay, the exception should be thrown — but by that time, Main has already printed the message and the application has exited.

</p>
</details>


---
###### 6. What's the output?

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

- A: Task started, waiting for it to complete...
     Something went wrong
- B: Something went wrong
- C: Task started, waiting for it to complete...
- D: The application crashes

<details><summary><b>Answer</b></summary>
<p>

#### Answer: C
Because the async Task method runs in the background (it is not awaited) the Main method finishes immediately after the Console.WriteLine.
Since the exception is thrown after Main is already done, it has no place to be caught, and the try-catch block around the var task = ... line is useless.

</p>
</details>

---
###### 7. What's the output?

```csharp

class Program
{
    static void Main()
    {
        int[] numbers = [ 1, 2, 3 ];

        ModifyArray(numbers);
        Console.WriteLine(string.Join(", ", numbers));

        ResetArray(numbers);
        Console.WriteLine(string.Join(", ", numbers));
    }

    static void ModifyArray(int[] arr)
    {
        arr[0] = 99;
    }

    static void ResetArray(int[] arr)
    {
        arr = [0,0,0];
    }
}

```

- A: <br>1, 2, 3<br>
     1, 2, 3
- B: <br>1, 2, 3<br>
     0, 0, 0
- C: <br>99, 2, 3<br>
     0, 0, 0
- D: <br>99, 2, 3<br>
     99, 2, 3

<details><summary><b>Answer</b></summary>
<p>

#### Answer: D
In ResetArray, arr is a copy of the reference to numbers.
When we assign arr = [ 0, 0, 0 ], we are changing the local variable arr to point to a new array. But this change <b>is not visible to the caller</b>. The original numbers array is unchanged. So numbers still points to the array with values: 99, 2, 3.

</p>
</details>


---
###### 8. What's the output?

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

- A: True, False, False
- B: True, True, True
- C: False, False, False
- D: True, True, False


<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
string a = "hello"; this is a string literal, so it's interned — stored in the intern pool. a points to that interned string.   
string b = "he" + "llo";
Both parts are string literals. Same interned string as a.   
string c = string.Concat("he", "llo");
This is evaluated at runtime. Even though the value is "hello", the result is not interned automatically.
So c refers to a new string instance with the same content.   
string d = new string("hello".ToCharArray());
This explicitly creates a new string object using a char[].
It will never refer to the interned version unless you manually intern it using string.Intern().

</p>
</details>



---
###### 9. What's the output?

```csharp

class Program
{
    static void Main()
    {
        string a = "hello";
        string b = a;
        string c = "hello";
        string d = new string("hello".ToCharArray());

        Console.WriteLine(object.ReferenceEquals(a, b));
        Console.WriteLine(object.ReferenceEquals(a, c));
        Console.WriteLine(a == d);
        Console.WriteLine(a.Equals(d));
    }
}

```

- A: True, False, True, True
- B: True, True, True, True
- C: True, True, True, False 
- D: False, False, True, True


<details><summary><b>Answer</b></summary>
<p>

#### Answer: B

True     -  a and b point to the same reference  
True     -  a and c are both interned "hello"  
True     -  a and d have same content (== is overridden)  
True     -  Equals() checks content equality  


</p>
</details>


---
###### 10. What's the output?

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

- A: 10
- B: Use of unassigned local variable 'b'
- C: Runtime Error
- D: 0

<details><summary><b>Answer</b></summary>
<p>

#### Answer: D

Static fields in C# are initialized in the order they are declared in the class, not in the order of their references in the code. 
a is declared first, and it is initialized with the value of b. 
However, b is declared after a, and its default value is 0 (because it's an int and the default value for int is 0).


</p>
</details>


---
###### 11. What's the output?

```csharp

class Counter
{
    public static int Count = 0;

    public static void Increment()
    {
        for (int i = 0; i < 1_000_000; i++)
        {
            Count++;
        }
    }
}

class Program
{
    static async Task Main()
    {
        var task1 = Task.Run(() => Counter.Increment());
        var task2 = Task.Run(() => Counter.Increment());

        await Task.WhenAll(task1, task2);

        Console.WriteLine($"Final Count: {Counter.Count}");
    }
}


```

- A: Final Count: 1000000
- B: Final Count: 2000000
- C: More than 2000000
- D: Less than 2000000

<details><summary><b>Answer</b></summary>
<p>

#### Answer: D

The Count++ operation is not thread-safe, as it involves a read-modify-write sequence that can be interrupted by other threads.  
Since both tasks run concurrently, increments may overlap, leading to lost updates and a final count less than 2000000. This happens because the static field is shared across threads, and no synchronization (like Interlocked or lock) is used.


</p>
</details>



---
###### 12. What's the output?

```csharp

class Program
{
    static async Task Main()
    {
        try
        {
            ThrowingTask();
            Console.WriteLine("Main continues...");
            await Task.Delay(2000);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Caught: {ex.Message}");
        }
    }

    static async Task ThrowingTask()
    {
        await Task.Delay(500);
        throw new InvalidOperationException("Boom!");
    }
}


```

- A: Main continues.. Caught: Boom!
- B: Main continues..
- C: No output
- D: Main continues.. Application crashes

<details><summary><b>Answer</b></summary>
<p>

#### Answer: B

The exception in ThrowingTask is thrown on a background thread, but since the task is <b>not awaited</b>, the exception is never observed by the Main method’s try-catch. Unobserved task exceptions can crash the application or be silently ignored, depending on the runtime and .NET version. This is why fire-and-forget tasks should be avoided unless properly handled, e.g., with logging or exception capturing.


</p>
</details>


---
###### 13. What's the output?

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

- A: 2, 4, 6, 4, 5
- B: 1, 4, 6, 8, 5
- C: 1, 2, 3, 4, 5
- D: Compile-time error: cannot slice an array with Span

<details><summary><b>Answer</b></summary>
<p>

#### Answer: B
Span<T> provides a <b>view</b> over the original memory, not a copy. In this case, numbers.AsSpan(1, 3) creates a span covering elements at index 1 to 3 (values 2, 3, 4), and the loop doubles those values in-place. Because Span<T> directly references the original array memory, any changes to it reflect in the original array, making it both efficient and safe for high-performance scenarios without additional allocations.
</p>
</details>


---
###### 14. What's the output?

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

- A: Runtime error
- B: Derived.Show
- C: No Output
- D: Base.Show

<details><summary><b>Answer</b></summary>
<p>

#### Answer: D
Base declares a virtual method Show().
Derived hides (not overrides!) the Show() method by declaring public void Show() without using the override keyword.
At runtime, you're calling obj.Show() where obj is of type Base — and since method hiding (via new) is resolved by the compile-time type, Base.Show() is called, not Derived.Show().

To get polymorphic behavior (Derived.Show()), the method in Derived must be marked with override. Otherwise, it hides the base method, and calls are resolved by the reference type — not the instance type.</p>
</details>



---
###### 15. What's the output?

```csharp

class Base
{
    public static void WhoAmI() => Console.WriteLine("Base.Static");
    public virtual void Identify() => Console.WriteLine("Base.Instance");
}

class Derived : Base
{
    public new static void WhoAmI() => Console.WriteLine("Derived.Static");
    public override void Identify() => Console.WriteLine("Derived.Instance");
}

class Program
{
    static void Main()
    {
        Derived d = new Derived();
        Base b = d;

        Derived.WhoAmI();
        Base.WhoAmI();
        d.Identify();
        b.Identify();
    }
}

```

- A: Derived.Static Base.Static Derived.Instance Derived.Instance
- B: Base.Static Base.Static Derived.Instance Derived.Instance
- C: Derived.Static Base.Static Base.Instance Derived.Instance
- D: Compiler error

<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
Derived.WhoAmI() calls the static method defined in Derived, which hides the one in Base, so it prints "Derived.Static".  
Base.WhoAmI() calls the static method from Base, because <b>static methods are resolved at compile time based on the reference type</b>, not the runtime object type.  
Both d.Identify() and b.Identify() call the <b>overridden</b> Identify method from Derived, because instance virtual methods are resolved at runtime based on the actual object type (Derived), so both print "Derived.Instance".
</p>
</details>


---
###### 16. What's the output?

```csharp

class Base
{
    static Base()
    {
        Console.WriteLine("Static Base");
    }
    public Base()
    {
        Console.WriteLine("Instance Base");
    }
}

class Derived : Base
{
    private readonly string msg = PrintMsg();

    static Derived()
    {
        Console.WriteLine("Static Derived");
    }
    public Derived()
    {
        Console.WriteLine("Instance Derived");
    }
    private static string PrintMsg()
    {
        Console.WriteLine("Field Initialization");
        return "Done";
    }
}

class Program
{
    static void Main()
    {
        Derived d = new Derived();
    }
}


```

- A: Static Derived  
Static Base  
Field Initialization  
Instance Base  
Instance Derived

- B: Static Derived  
Field Initialization  
Static Base  
Instance Base  
Instance Derived  

- C: Static Base  
Field Initialization  
Static Derived  
Instance Base  
Instance Derived

- D: Field Initialization  
Static Base  
Static Derived  
Instance Base  
Instance Derived


<details><summary><b>Answer</b></summary>
<p>

#### Answer: B
Derived's static constructor runs first because it's the first type used in Main, even though it's a derived class — static constructors are triggered based on usage, not inheritance chain.
The instance field msg in Derived is initialized before the base constructor runs, so "Field Initialization" is printed next.
After that, Base's static constructor runs, followed by its instance constructor, and finally Derived's instance constructor. Base class instance constructors are <b>always called</b> before derived class constructors.
</p>
</details>



---
###### 17. What's the output?

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

- A: True
- B: False
- C: Runtime exception
- D: Compiler error


<details><summary><b>Answer</b></summary>
<p>

#### Answer: B
S is a value type (a struct), and when you assign it to object, <b>boxing</b> occurs — meaning the value is copied into a new object on the heap.
When you do object o1 = s; and then object o2 = s;, each boxing operation creates a new boxed copy of the value. 
So o1 and o2 refer to different objects, even though they hold the same value — and therefore, object.ReferenceEquals(o1, o2) returns false.
</p>
</details>


---
###### 18. What's the output?

```csharp

public class A { }

public static class Extensions
{
    public static void Foo(this A a) => Console.WriteLine("Extension");
}

public class B : A
{
    public void Foo() => Console.WriteLine("Instance");
}

class Program
{
    static void Main()
    {
        A obj = new B();
        obj.Foo();
    }
}

```

- A: Compiler error
- B: Extension
- C: Instance
- D: Runtime exception

<details><summary><b>Answer</b></summary>
<p>

#### Answer: B
B defines an instance method Foo(), but obj is declared as type A, and A does not have a method called Foo().
Since A has no Foo() instance method, the compiler uses the extension method defined in Extensions.
Even though obj is actually a B at runtime, method resolution for extension methods is static, based on the compile-time type (A), not the runtime type (B). So Extension is printed.
</p>
</details>


---
###### 19. What's the output?

```csharp

public interface IAnimal { }

public class Dog : IAnimal { }

public static class Extensions
{
    public static void Speak<T>(this T animal) where T : IAnimal
    {
        Console.WriteLine("Woof!");
    }

    public static void Speak(this object obj)
    {
        Console.WriteLine("Generic object");
    }
}

class Program
{
    static void Main()
    {
        object dog = new Dog();
        dog.Speak();
    }
}

```

- A: Compiler error
- B: Woof
- C: Generic object
- D: Runtime exception

<details><summary><b>Answer</b></summary>
<p>

#### Answer: C
The output is <b>Generic object</b> because the variable dog is declared as object, not as Dog or IAnimal. Extension methods are resolved at compile time based on the static type of the variable, and object does not meet the generic constraint where T : IAnimal. Therefore, the compiler picks the non-generic Speak(this object obj) extension method.
</p>
</details>


---
###### 20. What's the output?

```csharp

class Program
{
    static ref int Find(ref int x, ref int y)
    {
        if (x > y)
            return ref x;
        else
            return ref y;
    }

    static void Main()
    {
        int a = 5, b = 10;
        ref int max = ref Find(ref a, ref b);
        max = 100;
        Console.WriteLine($"{a}, {b}");
    }
}

```

- A: 100, 10
- B: 5, 100
- C: 5, 10
- D: 100, 100

<details><summary><b>Answer</b></summary>
<p>

#### Answer: B
The method Find returns a <b>reference</b> to either x or y, depending on which is greater. Since b (10) is greater than a (5), Find returns a reference to b, and max becomes a reference to b. Setting max = 100 changes the value of b, so the final output is 5, 100.
</p>
</details>

---
###### 21. What's the output?

```csharp

class Program
{
    static ref int Select(ref int a, out int b)
    {
        b = a;
        return ref a;
    }

    static void Main()
    {
        int x = 1;
        ref int y = ref Select(ref x, out int z);
        y = 5;
        Console.WriteLine($"{x} {z}");
    }
}

```
- A: 5 5
- B: 1 1
- C: 5 1
- D: 1 5

<details><summary><b>Answer</b></summary>
<p>

#### Answer: C
When Select is called, it assigns b = a, so z gets the original value of x, which is 1. 
Then Select returns a reference to x, and y is a ref to x, so changing y = 5 actually modifies x to 5.  
Thus, the final output is 5 1, because x became 5, but z (copied before modification) remains 1. 
</p>
</details>


---
###### 22. What's the output?

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
- A: 4 16
- B: 100 144
- C: Nothing (empty output)
- D: Throws an exception

<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
LINQ uses <b>deferred execution</b>, meaning the query is not evaluated when defined, but only when enumerated inside foreach.  
However, numbers was reassigned to a new range (10–14), and the query still refers to the <b>old enumerable (1–5)</b> because Enumerable.Range(1,5) produces an immutable sequence.  
Thus, the original even numbers 2 and 4 are selected, squared to 4 and 16, and the output is 4 16.
</p>
</details>


---
###### 23. What's the output?

```csharp

class Program
{
    static void Main()
    {
        int counter = 0;
        var query = Enumerable.Range(1, 3).Select(x => counter++);

        foreach (var item in query.Reverse())
            Console.Write(item);
    }
}


```
- A: Throws an exception
- B: 210
- C: 012
- D: 222

<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
The LINQ query uses deferred execution, so counter++ is not evaluated until the query is enumerated.  
When query.Reverse() is executed, it forces evaluation of Select, incrementing counter as it maps 0, 1, 2 (in order), and then reverses the result to 2, 1, 0.
</p>
</details>



---
###### 24. What's the output?

```csharp

class Program
{
    static void Main()
    {
        var actions = Enumerable.Range(1, 3)
            .Select(i => new Action(() => Console.Write(i)))
            .ToArray();

        foreach (var action in actions)
            action();
    }
}


```
- A: 333
- B: 123
- C: 111
- D: 321

<details><summary><b>Answer</b></summary>
<p>

#### Answer: A or B (depending on C# version)
The lambda () => Console.Write(i) captures the loop variable i from the LINQ Select statement.  
In older C# versions (pre-C# 5), all the lambdas would share the same captured i, resulting in 333.  
In modern C#, LINQ with Select(i => ...) creates a new i per iteration (no closure issue here), so each Action correctly captures a distinct value.  
Therefore, when each Action in the array is invoked, it prints 1, 2, then 3.
</p>
</details>


---
###### 25. What's the output?

```csharp

class Program
{
    static async Task Main()
    {
        var t1 = Task.Delay(1000).ContinueWith(_ => "First");
        var t2 = Task.Delay(500).ContinueWith(_ => "Second");
        var t3 = Task.Delay(2000).ContinueWith(_ => "Third");

        var firstDone = await Task.WhenAny(t1, t2, t3);
        Console.WriteLine(await firstDone);

        var all = await Task.WhenAll(t1, t2, t3);
        Console.WriteLine(string.Join(",", all));
    }
}


```
- A: Second   
First,Second,Third
- B: Second  
Third,Second,First
- C: Second  
Second,First,Third
- D: First  
Third,Second,First

<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
Task.WhenAny(t1, t2, t3) returns the first completed task (not necessarily in the order written). t2 has the shortest delay (500ms), so it finishes first.
Therefore, firstDone becomes t2. await firstDone unwraps the result of t2, which is "Second".  

Task.WhenAll(t1, t2, t3) waits for all tasks to finish and gathers their results in <b>the original order</b> they were defined (t1, t2, t3). 
So the second output is: "First,Second,Third".
</p>
</details>



---
###### 26. What's the output?

```csharp

var numbers = Get();

if (numbers.Any())
{
    Console.WriteLine("Looping");
    foreach (var n in numbers)
        Console.WriteLine(n);
}

IEnumerable<int> Get()
{
    Console.WriteLine("Generating");
    yield return 1;
    yield return 2;
}


```
- A: Generating  
Looping  
1  
2
- B: Generating  
Looping  
Generating  
1  
2
- C: Looping  
1  
2
- D: Looping

<details><summary><b>Answer</b></summary>
<p>

#### Answer: B
The numbers variable holds an IEnumerable<int> produced by Get(), which uses yield return, meaning it's lazily evaluated.  
The first call to numbers.Any() triggers enumeration, causing "Generating" to print once as it starts iterating to check if any elements exist.  
The second enumeration in the foreach loop restarts the iterator, so "Generating" is printed again before yielding the values 1 and 2.
</p>
</details>


---

###### 27. What's the output?

```csharp
public class Program
{
    public static void Main()
    {
        List<int> numbers = new List<int>{1, 2, 3, 4, 5};

        var result = numbers.Where(n => n % 2 == 0).Select(n => n * n).Aggregate((a, b) => a + b);
        Console.WriteLine(result);
    }
}

```

- A: 20
- B: 14
- C: 10
- D: 4
  
<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
Where(n => n % 2 == 0) filters even numbers: {2, 4}  
Select(n => n * n) squares them: {4, 16}  
Aggregate((a, b) => a + b) adds them: 4 + 16 = 20
</p>
</details>

---

###### 28. What's the output?

```csharp
public class Publisher
{
    public delegate void Notify();
    public event Notify ProcessCompleted;

    public void RaiseEvent()
    {
        ProcessCompleted?.Invoke();
    }
}

public class ExternalInvoker
{
    public void TryInvokeEvent(Publisher publisher)
    {
        publisher.ProcessCompleted(); 
    }
}

public class Program
{
    public static void Main()
    {
        Publisher pub = new Publisher();
        pub.ProcessCompleted += () => Console.WriteLine("Process Completed!");

        ExternalInvoker invoker = new ExternalInvoker();
        invoker.TryInvokeEvent(pub);
    }
}

```

- A: The event is successfully invoked.
- B: The code runs, but the event is never triggered.
- C: The code compiles but throws a runtime exception.
- D: The code fails to compile with an error about event access.
  
<details><summary><b>Answer</b></summary>
<p>

#### Answer: D
Even though ProcessCompleted is a public event, C# <b>restricts event invocation to the class that declares it (or its derived classes)</b>.
Attempting to invoke it from outside the declaring class (as in ExternalInvoker) results in a <b>compile-time error</b>.
</p>
</details>


---

###### 29. What's the output?

```csharp
Func<int> f1 = () => { Console.Write("1"); return 10; };
Func<int> f2 = () => { Console.Write("2"); return 20; };
var f = f1 + f2;

int result = f();
Console.Write($":{result}");

```

- A: 12:10
- B: 21:20
- C: 12:20
- D: 12:30
  
<details><summary><b>Answer</b></summary>
<p>

#### Answer: C
In C#, multicast delegates (created using +) invoke all combined methods, but only the result of the last delegate is returned.
f1 prints "1" and returns 10. f2 prints "2" and returns 20.

When calling f(), both f1 and f2 are invoked, in order (f1, then f2), so Console.Write("1") then Console.Write("2") happens — output: 12. 
f() returns the result of f2, which is 20, and that is printed after the colon.
</p>
</details>


---

###### 30. What's the output?

```csharp
class A { }
class B : A { }

class Program
{
    static void Print<T>(T item) where T : A => Console.WriteLine("Generic");
    static void Print(A item) => Console.WriteLine("Non-generic");

    static void Main()
    {
        B b = new B();
        Print(b);
    }
}


```

- A: Generic
- B: Non-generic
- C: Compiler error
- D: Runtime error
  
<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
When the method Print(b) is called with an object of type B, the compiler considers both the generic method Print<T>(T item) and the non-generic method Print(A item). 
The method Print<T>(T item) can be used because B is a subclass of A, and it matches the constraint where T : A. The compiler will infer T as B, making it a valid call for the generic method.  
In C#, the generic method is preferred over the non-generic one when the generic method's type constraint is satisfied. In this case, B satisfies T : A, so the generic method is chosen.
</p>
</details>


---

###### 31. What's the output?

```csharp
interface IProducer<T>
{
    T Produce();
}

class Animal { public override string ToString() => "Animal"; }
class Dog : Animal { public override string ToString() => "Dog"; }

class DogProducer : IProducer<Dog>
{
    public Dog Produce() => new Dog();
}

class Program
{
    static void Main()
    {
        IProducer<Dog> dogProducer = new DogProducer();
        IProducer<Animal> animalProducer = dogProducer;
        Console.WriteLine(animalProducer.Produce());
    }
}


```

- A: Compilation error
- B: Runtime InvalidCastException
- C: Prints "Dog"
- D: Prints "Animal"
  
<details><summary><b>Answer</b></summary>
<p>

#### Answer: A
In C#, generic interfaces are <b>not covariant</b> by default unless explicitly declared with the out keyword on the type parameter.
IProducer<T> interface is declared as interface IProducer<T>, without covariance. Therefore, even though Dog derives from Animal, IProducer<Dog> cannot be assigned to IProducer<Animal>. 

So the line: ` IProducer<Animal> animalProducer = dogProducer;`
causes a compile-time error: "Cannot implicitly convert type `IProducer<Dog>` to `IProducer<Animal>`." 
To make this code compile the interface should be declared as: `interface IProducer<out T>`
</p>
</details>
