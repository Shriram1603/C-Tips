# Interview Questions

# Question 1: Boxing/Unboxing

```csharp
int x = 100;
object obj = x;     // Boxing
x = 200;
int y = (int)obj;   // Unboxing
Console.WriteLine(y); // Output?

```
`Answer`: `100`


`Explanation`: When x is boxed, the value 100 is copied into a new object on the heap. Changing x to 200 later does not affect the already-boxed value. So unboxing gives back the original 100.

---

# Question 2: Type Casting

```csharp
double val = 66.9;
char ch = (char)val;
Console.WriteLine(ch);
```
`Answer`: `B`


`Explanation`: (char)66.9 => 66 (decimal truncation). ASCII 66 is 'B'

---

# Question 3: Implicit vs Explicit Casting

```csharp
int a = 300;
byte b = (byte)a;
Console.WriteLine(b);
```
`Answer`: `44`


`Explanation`:

- int is 32-bit, byte is 8-bit (0–255).
- 300 > 255, so overflow occurs.
- C# does not throw an error on narrowing cast—it silently wraps around:

```csharp
300 % 256 = 44
```
---

# Question 4: Reference Type Behavior


```csharp
object obj1 = "Hello";
object obj2 = obj1;
obj1 = "World";
Console.WriteLine(obj2);
```
`Answer`: `Hello`


`Explanation`:
- string is a reference type, but immutable.

- obj1 = "World"; makes obj1 point to a new string, does not affect obj2.

- obj2 still points to "Hello".

  ---

# Question 5: Nullable and Boxing

```csharp
int? n = null;
object o = n;
Console.WriteLine(o == null);
```

`Answer`: `True`


`Explanation`:

- int? n = null; — nullable is null.

- When boxed, nullable types preserve null.

- So object o = n; actually makes o == null.

---

# Question 6: Struct Mutation via Interface

```csharp
struct MyStruct : IMyInterface {
    public int Value;
    public void Update() {
        Value = 42;
    }
}
interface IMyInterface { void Update(); }

class Program {
    static void Main() {
        MyStruct s = new MyStruct();  // s.Value == 0
        IMyInterface i = s;           // **boxes** s into a heap object
        i.Update();                   // calls Update() on the **boxed copy**
        Console.WriteLine(s.Value);   // ?
    }
}

```

`Answer`: `0`


`Explanation`:

Assigning the struct to IMyInterface boxes it—creates a separate copy on the heap. Calling Update() changes the boxed copy’s Value to 42, but the original s remains unchanged (still zero).

---

# Question 7:  Implicit Casting Precision Loss

```csharp
float f = 1.23456789f;
double d = f;
Console.WriteLine(d);
```

`Answer`: `1.2345678806304932` (your exact runtime output may vary slightly)


`Explanation`:

- A float has about 7 decimal digits of precision. When you write 1.23456789f, the compiler rounds to the nearest representable float (≈ 1.2345678806…).

- Converting that float to double preserves that imprecise value—it does not regain precision.

- Compare to double d = 1.23456789;
- That literal is parsed as a 64-bit double directly, so it holds more precision (about 15 digits) and will not match the float → double conversion.

---

# Question 8:  Nullable Comparison Logic

```csharp
int? a = null;
int? b = null;
Console.WriteLine(a == b);
Console.WriteLine(a.Equals(b));

```

`Answer`: `True, True`


`Explanation`:

- == for Nullable<T> is overloaded so that if both are null, it returns true.

- Equals(…) on a Nullable<T> checks HasValue; if false it returns other == null, so it also yields true when both are null.

---

# Question 9:  Object Assignment – Struct vs Class

```csharp
public struct Point { public int X; }

class Program {
    static void Main() {
        Point p1 = new Point { X = 5 };
        object obj = p1;   
        p1.X = 10;         
        Point p2 = (Point)obj; 
        Console.WriteLine(p2.X);
    }
}
```

`Answer`: `5`


`Explanation`:

- The boxed object's value has a new reference and remains unchanged; thus, unboxing it will only have a value of 5.

---

# Question 10:  Object Assignment – Boxing and is Pattern Matching

```csharp
int? num = null;
if (num is int i)
    Console.WriteLine($"i: {i}");
else
    Console.WriteLine("num is not an int");

```

`Answer`: `"num is not an int"`


`Explanation`:

- num is not an int", the is will only assign int value to the variable "i" and "i" is not nullable and null cannot be assigned

---

# **Q11. What is the default value of `int?` and how is it different from `int`?**

**Answer:**  
`int? = null` and `int = 0`; nullable types default `HasValue` is false → default value is `null`, and value types like `int` have well-defined defaults, so `0`.

---

### **Q12. Explain how `Nullable<T>` is internally implemented in C#. Why can't `Nullable<T>` be used with reference types?**

**Answer:**  
`Nullable<T>` has `Value` and `HasValue` properties which emulate null values just like references. `HasValue = false` => null. `Nullable<T>` is a struct and it only accepts struct values.  
`Nullable<T> where T : struct`. References don’t need `Nullable<T>` to emulate null — they use null pointers.

---

# **Q13. Given the code below, what will be the output? Why?**

```csharp
int? a = null;
int b = a ?? 5;
Console.WriteLine(b);
```

**Answer:**  
5 — Null-coalescing operator compares and assigns the right-side value if the left is null.

---

# **Q14. What's the purpose of the `?.` operator and how is it different from `??`? Give an example.**

**Answer:**  
`?.` suppresses null reference exceptions.  
`??` compares the left value with null — if it’s null, assigns the right-side value.

---

# **Q15. Consider this code:**

```csharp
int? a = null;
if (a == null)
    Console.WriteLine("Null");
else
    Console.WriteLine("Has Value");
```

**Answer:**  
"Null", compares HasValue internally; `HasValue = false` means it’s null.

---

# **Q16. What is the output of this code? Why?**

```csharp
int? a = null;
int? b = 10;
int? result = a + b;
Console.WriteLine(result);
```

**Answer:**  
null — arithmetic with a null value results in null.

---

# **Q17. When would you use `GetValueOrDefault()` instead of `??`? Can you give an example where they behave differently?**

**Answer:**  
Use `GetValueOrDefault()` when you want to use the type’s default value.  
Use `??` when you want to provide a custom fallback.

---

# **Q18. Is this valid code? Why or why not?**

```csharp
Nullable<string> s = null;
```

**Answer:**  
No, because `Nullable<T>` accepts only structs, not reference types.

---

# **Q19. In what real-world scenarios would you prefer using a nullable value type over a default value (like 0 or false)?**

**Answer:**  
When setting the age of a person in a database, `0` would be incorrect. `null` indicates the value hasn’t been set.

---

# **Q20. True or False: `Nullable<T>` is a class. Justify your answer.**

**Answer:**  
False. `Nullable<T>` is a struct that makes other structs nullable — it is a value type stored on the stack.

---


## Q21: `Span<T>` and Memory Performance

```csharp
public static void Main()
{
    int[] data = { 1, 2, 3, 4, 5 };
    Span<int> span = data;
    span[0] = 42;
    Console.WriteLine(data[0]);
}
```

**❓What will be the output? What advantage does `Span<T>` offer here?**

**✅ Answer:**  
Output: `42`  
`Span<T>` provides a memory-safe, efficient view over contiguous memory (like arrays, stackalloc, etc.). It's allocated on the stack and **avoids heap allocations**, making it ideal for performance-critical scenarios like parsing or slicing arrays.

---

## Q22: `ref`, `in`, and `out` Parameter Behavior

```csharp
public static void Modify(ref int x, in int y, out int z)
{
    x = x + 10;
    // y = y + 1; // ❌ Compile error: Cannot assign to 'in' parameter
    z = x + y;
}

public static void Main()
{
    int a = 5, b = 3, c;
    Modify(ref a, in b, out c);
    Console.WriteLine($"{a}, {b}, {c}");
}
```

**❓What will be the output and why does `in` give a compile error?**

**✅ Answer:**  
Output: `15, 3, 18`  
- `ref` allows both read/write.
- `in` allows only **readonly by reference**.
- `out` must be assigned inside the method.

---

## Q23: `yield return` Execution Trap

```csharp
public static IEnumerable<int> Generate()
{
    Console.WriteLine("Before yield");
    yield return 1;
    Console.WriteLine("After yield");
}

public static void Main()
{
    var gen = Generate();
    Console.WriteLine("Main Start");
    foreach (var i in gen)
    {
        Console.WriteLine(i);
    }
}
```

**❓What will be printed and why is `yield return` deferred?**

**✅ Answer:**  
Output:
```
Main Start  
Before yield  
1  
After yield
```

`yield return` is part of an iterator. The function **pauses** after `yield return` and **resumes** on next iteration. Execution is deferred until enumeration.

---

## Q24: Custom `IEnumerable` Implementation

```csharp
public class MyRange : IEnumerable<int>
{
    int start, count;
    public MyRange(int start, int count) { this.start = start; this.count = count; }

    public IEnumerator<int> GetEnumerator()
    {
        for (int i = 0; i < count; i++)
            yield return start + i;
    }

    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
}

public static void Main()
{
    foreach (int i in new MyRange(10, 3))
        Console.WriteLine(i);
}
```

**❓What will be the output? Why is `IEnumerable` useful here?**

**✅ Answer:**  
Output:
```
10  
11  
12
```

Custom `IEnumerable` enables user-defined sequences to be iterated using `foreach`. It leverages `yield return` internally to simplify state management.

---

## Q25: `readonly struct` and `ref struct` Pitfalls

```csharp
readonly struct ReadOnlyPoint
{
    public int X { get; }
    public ReadOnlyPoint(int x) => X = x;
}

ref struct StackOnly
{
    public int X;
}

public static void Main()
{
    ReadOnlyPoint p = new ReadOnlyPoint(5);
    // Console.WriteLine(p.X = 10); // ❌ Error: Property has no setter

    Span<int> span = stackalloc int[5];
    StackOnly s; // ✅ OK: lives on stack
}
```

**❓Why are `readonly struct` and `ref struct` restricted like this?**

**✅ Answer:**  
- `readonly struct` prevents mutation and ensures immutability, especially important for performance and predictable behavior.
- `ref struct` can **only live on the stack** and cannot be boxed or captured by lambdas. This ensures **safety and performance** for stack-only scenarios like `Span<T>`.



## Q26: Multicast Delegate Return Value

```csharp
public delegate int MyDelegate();
public static int A() { Console.WriteLine("A"); return 1; }
public static int B() { Console.WriteLine("B"); return 2; }

public static void Main()
{
    MyDelegate d = A;
    d += B;
    int result = d();
    Console.WriteLine($"Result: {result}");
}
```

**❓What will be printed and why?**

**✅ Answer:**  
```
A  
B  
Result: 2
```

All methods in the multicast delegate are invoked, but **only the return value of the last method (`B`) is returned**.

---

## Q27: LINQ and Deferred Execution with Delegates

```csharp
List<int> numbers = new List<int> { 1, 2, 3 };
var query = numbers.Select(x => x * 2);
numbers.Add(4);

foreach (var item in query)
    Console.WriteLine(item);
```

**❓What will be printed and why is this related to delegates?**

**✅ Answer:**  
```
2  
4  
6  
8
```

LINQ uses **deferred execution** powered by delegates (`Func<T, TResult>`). The delegate captures the logic, and the query is evaluated **only when enumerated**.

---

## Q28: Closure and Delegate Capture

```csharp
var actions = new List<Action>();
for (int i = 0; i < 3; i++)
{
    actions.Add(() => Console.WriteLine(i));
}
foreach (var action in actions)
    action();
```

**❓What will be printed and why?**

**✅ Answer:**  
```
3  
3  
3
```

The delegate **captures the variable `i`**, not its value at the time of creation. After the loop, `i == 3`, so all actions print `3`.

---

## Q29: Async/Await with Event Handler

```csharp
public static async Task AsyncMethod()
{
    Console.WriteLine("Inside Method");
    await Task.Delay(500);
    Console.WriteLine("After Await");
}

public static async Task Main()
{
    Console.WriteLine("Start");
    var task = AsyncMethod();
    Console.WriteLine("Middle");
    await task;
    Console.WriteLine("End");
}
```

**❓What is the order of execution and why?**

**✅ Answer:**  
```
Start  
Middle  
Inside Method  
After Await  
End
```

`AsyncMethod()` starts, but `await` inside it yields control. `Main` continues execution after calling the method, then resumes after the `await`.

---

## Q30: Null-Conditional Invocation of Events

```csharp
public event EventHandler Click;

public void Raise()
{
    Click?.Invoke(this, EventArgs.Empty);
    Console.WriteLine("Event invoked");
}
```

**❓What happens if no handlers are subscribed and `Raise()` is called?**

**✅ Answer:**  
Nothing breaks. Output:
```
Event invoked
```

The null-conditional operator (`?.`) ensures `Click.Invoke` is only called if it's not null. If there are no subscribers, it safely skips invocation.


## Q31: Delegate Combination

```csharp
int counter = 0;

Action a = () => counter++;
Action b = () => counter++;
Action c = a + b + a;
c();
Console.WriteLine(counter);

```

**✅ Answer:**  
`Output`: `3`


📌 Explanation:
When you combine delegates with +, you're building a multicast delegate.

- c = a + b + a means:
    - Run a → counter++ (1)

    - Run b → counter++ (2)

    - Run a again → counter++ (3)

✅ It's not about closure here — it's just that c() executes 3 actions in order.

## Q32: Boxing a struct that implements an interface

```csharp
interface ILogger
{
    void Log();
}

struct MyLogger : ILogger
{
    public void Log() => Console.WriteLine("Logging...");
}
```

**✅ Answer:**  
`Output`: `Logging...`


📌 Explanation:
- When you combine delegates with +, you're building a multicast delegate.

- When assigning a struct to an interface (ILogger logger = new MyLogger();), boxing does occur — a copy is stored on the heap.

- But that copy still retains the method implementation — so Log() will print "Logging...".
✅ So there is boxing, but you still get output.


## Q33: Object Comparison

```csharp
object a = 5;
object b = 5;

Console.WriteLine(a == b);        // false
Console.WriteLine(a.Equals(b));   // true

```

**✅ Answer:**  
`Output`: `false , true`


📌 Explanation:
- a == b: Reference comparison → false (both boxed separately).

- a.Equals(b): Value comparison via int.Equals(int) → true.

## Q34: Difference between ref readonly and readonly ref



**✅ Answer:**  


These are actually the same thing — just different syntactic orders. Both declare a read-only reference to a value.

```csharp
ref readonly int GetRefReadonly() => ref someArray[0];
```

📌 Explanation:
This means:

You're returning a reference (ref) to an int

That reference is read-only — you cannot write to it

🔹 Why it's used:
To return large structs by reference (to avoid copying), but still prevent modification.

✅ So:

ref readonly = readonly ref = reference that can't be written to.

