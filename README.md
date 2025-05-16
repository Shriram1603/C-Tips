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
