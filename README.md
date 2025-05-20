# C-Tips

# 1. "=="

Checks only Value equality

```csharp
string s = "Hello World!";
string s2 = "Hello World!";
Console.WriteLine(s==s2);        // True
Console.WriteLine(object.ReferenceEquals(s,s2)); // True
string s3 = new("Hello World!");
Console.WriteLine(s == s3); // True
Console.WriteLine(object.ReferenceEquals(s,s3)); // False
```

# 2. Boxing and UnBoxing

`Boxing` : Converts Value type to refernce type. 
`UnBoxing` : Converts the boxed refernce type to its original value.

```csharp
int num = 23;         // value type is int and assigned value 23

Object Obj = num;    // Boxing

int i = (int)Obj;    // Unboxing
```

# 3. Value Types conversion

Value types can be upcasted or downcated to another value type without error , when down casted there will be some loss
in the data.

```csharp
double a = 65.222;  
char c = 'A';
int d = c;
Console.WriteLine(d); // output  65
char e = (char) a;
Console.WriteLine(e);  // output A
int b = (int) a;  // output 65
Console.WriteLine(b);
```

# 4. Array List

- Found in the System.Collections namespace.

- Stores elements as object, so you can add any type of data.

- Because it stores objects, you need boxing/unboxing for value types (e.g., int, double), which is inefficient.

- `Generics` `<T>` cannot be used with `ArrayList`. 

```csharp

ArrayList list = new ArrayList();
list.Add(10);        // boxing
list.Add("hello");   // works fine, but no type safety
int x = (int)list[0]; // unboxing

```

- `ArrayList` uses boxing and unboxing when storing value types like int, double, bool, etc.
  
- ArrayList stores elements as object references.

- Value types (like int) are not objects—they must be boxed to be stored.

- When retrieving the value, it must be unboxed back to the original type.

# 5. Passing objects as parameters

- When an array is passed to a method in C#, the method receives a copy of the reference to the array — not the actual array and not the actual variable holding the reference. This means:

- You can modify the contents of the array inside the method, and those changes will affect the original array.

- But if you try to assign a new array to the parameter, this will only update the local copy of the reference — the original reference remains unchanged.

- To reassign a new array and reflect that outside the method, use the ref keyword.

```csharp

void ModifyArray(int[] arr)
{
    arr[0] = 99;          // ✅ Modifies original array
    arr = new int[] { 1, 2, 3 };  // ❌ Only modifies local reference
}

int[] myArray = { 0, 0, 0 };
ModifyArray(myArray);
Console.WriteLine(myArray[0]); 

void ModifyArrayRef(ref int[] arr)
{
    arr[0] = 99;          // ✅ Modifies original array
    arr = new int[] { 1, 2, 3 };  // ✅ modifies original array
}

int[] myArrayRef = { 0, 0, 0 };
ModifyArrayRef(ref myArrayRef);
Console.WriteLine(myArrayRef[0]); 
```

# 6. Throw vs Throw ex

In C#, when handling errors, you can re-throw an exception using either:

1. throw;

2. throw ex;

They both throw the same exception — but there's a big difference:

### 🔹 Case 1: Using `throw;`

```csharp
try
{
    int x = 0;
    int result = 10 / x; // ❌ DivideByZeroException
}
catch (Exception ex)
{
    Console.WriteLine("Caught in catch block");
    throw; // 🔁 Re-throws the same exception
}
```
- ✅ Keeps the original error location.

- The stack trace will show the error happened at 10 / x.

### 🔸 Case 2: Using throw ex;

```csharp
try
{
    int x = 0;
    int result = 10 / x;
}
catch (Exception ex)
{
    Console.WriteLine("Caught in catch block");
    throw ex; // ⚠️ Re-throws, but resets the error location to THIS LINE
}

```

- ❌ The stack trace will now say the error happened at throw ex, not 10 / x.

### 📸 Imagine a Stack Trace

✅ With throw;

```sql
System.DivideByZeroException: Attempted to divide by zero.
   at MyProgram.Main() in Program.cs: line 5  ← shows actual line of error

```

❌ With throw ex;:
```pgsql
System.DivideByZeroException: Attempted to divide by zero.
   at MyProgram.Main() in Program.cs: line 10 ← shows throw ex line, original lost

```

| Use         | Keeps Original Error Info? | When to Use                          |
| ----------- | -------------------------- | ------------------------------------ |
| `throw;`    | ✅ Yes                      | Re-throw inside `catch`              |
| `throw ex;` | ❌ No (stack trace reset)   | Avoid unless modifying the exception |

### Rule of Thumb : Always use throw; when you're not changing the exception.

# Indexing and ElementAt()

- 🟡 `List<T>` doesn’t need `ElementAt()` — you can access by index:

```csharp
List<int> nums = new List<int> { 10, 20, 30 };
int value = nums[1]; // ✅ 20
```
- but `ElementAt()` is needed for `IEnumerable<T>` that does not support indexing (like HashSet<T>, Queue<T>, IQueryable<T>, etc.).

### 🧠 Why `ElementAt()` Exists

`ElementAt()` is an extension method from LINQ.

```csharp
int value = myEnumerable.ElementAt(2);
```

It works on any IEnumerable<T>, even if the underlying collection doesn't support indexers (like [i]).

### When to use Which

| Scenario                                                            | Use `[i]` or `ElementAt(i)`? | Why?                             |
| ------------------------------------------------------------------- | ---------------------------- | -------------------------------- |
| You have a `List<T>` or array                                       | Use `[i]`                    | Faster (direct access via index) |
| You have an `IEnumerable<T>` like `HashSet`, `Queue`, or LINQ query | Use `ElementAt(i)`           | Because `[i]` is not supported   |
| You’re chaining LINQ queries                                        | `ElementAt(i)`               | More readable in query chains    |

# Slicing in C#

C# does not support true slicing like Python (e.g., list[1:3]). But in C# 8.0+, you can do:

```csharp
var subList = list[1..3]; // Requires List<T> and .NET Core 3+ / .NET 5+

int[] myArray1 = {1,2,3}
int[] myArray2 = myArray1[0..3];
Console.WriteLine(myArrayRef2[0]); // 1
Console.WriteLine(myArrayRef2[1]); // 2
Console.WriteLine(myArrayRef[2]);  // 3

string s4 = "Hello World!";
string s5 = s4[0..5];
Console.WriteLine(s5); // Hello
```

