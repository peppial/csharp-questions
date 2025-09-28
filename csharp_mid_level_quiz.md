# Mid+ Level C# Quiz Questions

Test your Mid+ C# knowledge with these carefully selected questions covering important concepts like async/await, LINQ, generics, inheritance, and more.



---
###### 1. What's the output?

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
###### 2. What's the output?

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
###### 3. What's the output?

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
###### 4. What's the output?

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
###### 5. What's the output?

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
###### 6. What's the output?

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
###### 7. What's the output?

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

#### Answer: B or D

The exception in ThrowingTask is thrown on a background thread, but since the task is <b>not awaited</b>, the exception is never observed by the Main method’s try-catch. Unobserved task exceptions can crash the application or be silently ignored, depending on the runtime and .NET version. This is why fire-and-forget tasks should be avoided unless properly handled, e.g., with logging or exception capturing.


</p>
</details>


---
###### 8. What's the output?

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
###### 9. What's the output?

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

###### 10. What's the output?

```csharp

class Program
{
    private static object sobject = new();

    private static void Write()
    {
        lock (sobject)
        {
            Console.WriteLine("test");
        }
    }

    static void Main(string[] args)
    {
        lock (sobject)
        {
            Write();
        }
    }
}

```

- A: The program will hang (deadlock)
- B: Runtime error
- C: Complication error
- D: test

<details><summary><b>Answer</b></summary>
<p>

#### Answer: D
The lock statement uses a <b>reentrant lock</b>, meaning the <b>same thread</b> can acquire the lock multiple times without deadlocking.
So, when Main locks sobject and calls Write(), which also locks sobject, it does not cause a deadlock.
The program safely prints "test" once.
</p>
</details>

