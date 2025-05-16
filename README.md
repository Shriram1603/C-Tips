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


