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

# Question 2: Type Casting

```csharp
double val = 66.9;
char ch = (char)val;
Console.WriteLine(ch);
```
`Answer`: `B`


`Explanation`: (char)66.9 => 66 (decimal truncation). ASCII 66 is 'B'

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

