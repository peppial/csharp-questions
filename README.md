<div align="center">
  <h1>C# Questions</h1>
</div>



<p><div align="center">This repo is created similary to Lydia Hallie famous repo
  <a href="https://github.com/lydiahallie/javascript-questions">javascript-questions</a>.</div><div align="center">Credits to her for the great idea and hard work! :heart:</div>
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

- A:    
     True   
     False   
     False
- B:    
     True   
     True   
     True
- C:    
     False   
     False   
     False
- D:    
     True   
     True   
     False

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
        Console.WriteLine(object.ReferenceEquals(a, d));
        Console.WriteLine(a == d);
        Console.WriteLine(a.Equals(d));
    }
}

```

- A:    
    True
    False  
    True  
    True
- B:    
    True
    True  
    True  
    True
- C:    
    True
    True  
    True  
    False
- D:    
    False
    False  
    True  
    True

<details><summary><b>Answer</b></summary>
<p>

#### Answer: B

True     // a and b point to the same reference  
True     // a and c are both interned "hello"  
True     // a and d have same content (== is overridden)  
True     // Equals() checks content equality  


</p>
</details>
