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
