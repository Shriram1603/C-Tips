# Delegates in C# - Complete Guide

## 📌 What is a Delegate?
A **delegate** in C# is a type-safe object that points to a method (or multiple methods) with a specific signature. It's similar to a function pointer in C/C++ but much safer.

```csharp
public delegate void Notify(string message);
```

This declares a delegate named `Notify` that can point to any method taking a `string` and returning `void`.

---

## ✅ Why Use Delegates?

- **Decoupling**: Call methods without knowing their implementation.
- **Plug-in behavior**: Pass different logic at runtime.
- **Multicast**: Execute multiple methods from one call.
- **Callbacks**: Notify when an operation completes.

---

## 🔹 Delegate Syntax Overview

```csharp
public delegate void MyDelegate(string message); // Declaration

public class Program
{
    static void Main()
    {
        MyDelegate del = ShowMessage; // Assignment
        del("Hello Delegate!");       // Invocation
    }

    static void ShowMessage(string msg) => Console.WriteLine(msg);
}
```

---

## 🔄 Types of Delegates

### 1. Single-cast Delegate
Points to one method.
```csharp
public delegate void ProcessDelegate(string input);

ProcessDelegate processor = Print;
processor("Data");

void Print(string input) => Console.WriteLine(input);
```

### 2. Multicast Delegate
Points to multiple methods.
```csharp
ProcessDelegate processor = Print;
processor += PrintUpper;

processor("Hello!");

void Print(string input) => Console.WriteLine("Print: " + input);
void PrintUpper(string input) => Console.WriteLine("Upper: " + input.ToUpper());
```

⚠️ If the delegate has a return type, only the **last method's** result is returned.

### 3. Generic Delegate
Avoids custom declarations.
```csharp
Func<int, int, int> add = (a, b) => a + b;
int result = add(2, 3); // 5
```

### 4. Anonymous Delegate
Declared inline.
```csharp
ProcessDelegate del = delegate (string msg) {
    Console.WriteLine("Anonymous: " + msg);
};
del("Hi");
```

### 5. Lambda Expression as Delegate
```csharp
ProcessDelegate del = msg => Console.WriteLine("Lambda: " + msg);
del("Hello");
```

---

## 🤔 Delegates vs Interfaces

| Feature       | Delegate                                   | Interface                                 |
|---------------|--------------------------------------------|--------------------------------------------|
| Represents    | A method signature                         | A contract (set of members)                |
| Use Case      | Callbacks, plugins, events                 | Abstraction, consistent class structure    |
| Flexibility   | More dynamic at runtime                    | More rigid, design-time use                |
| Type Safety   | Type-safe                                  | Type-safe                                  |

### 📌 Rule of Thumb
- Use **delegate** when passing **behavior** (method).
- Use **interface** when defining a **type’s structure**.

---

## 🛠️ Real-World Examples

### Plugin Logger
```csharp
public delegate void LogDelegate(string message);

public class Logger
{
    public void LogToConsole(string message) =>
        Console.WriteLine("[Console] " + message);

    public void LogToFile(string message) =>
        File.AppendAllText("log.txt", message + "\n");
}

LogDelegate log = logger.LogToConsole;
log += logger.LogToFile;
log("App started");
```

### Callback on Completion
```csharp
public delegate void WorkComplete(string result);

public class Worker
{
    public void DoWork(string task, WorkComplete callback)
    {
        Console.WriteLine("Working on: " + task);
        Thread.Sleep(500);
        callback("Finished " + task);
    }
}
```

---

## ❌ Pitfall: Multicast Delegate with Return Value
```csharp
public delegate int Compute(int x);

Compute compute = Double;
compute += Triple;

int result = compute(5); // Returns Triple(5), i.e. 15

int Double(int x) => x * 2;
int Triple(int x) => x * 3;
```

To get all return values:
```csharp
foreach (var func in compute.GetInvocationList())
{
    int res = ((Compute)func)(5);
    Console.WriteLine(res);
}
```

---

## 🔥 Async Delegates and Task

### ❌ Wrong Way – `Func<Task>` with `+=`
```csharp
Func<string, Task> handler = Method1;
handler += Method2;

await handler("Hello"); // Only awaits Method2
```

### ✅ Correct Way – Use List of Func<string, Task>
```csharp
List<Func<string, Task>> asyncHandlers = new()
{
    Method1,
    Method2
};

var tasks = asyncHandlers.Select(handler => handler("Message"));
await Task.WhenAll(tasks);
```

### Example Methods:
```csharp
static async Task Method1(string msg)
{
    await Task.Delay(500);
    Console.WriteLine("[Method1] " + msg);
}

static async Task Method2(string msg)
{
    await Task.Delay(100);
    Console.WriteLine("[Method2] " + msg);
}
```

### GetInvocationList Alternative
```csharp
foreach (var handler in notify.GetInvocationList())
{
    await ((Func<string, Task>)handler)("Hello");
}
```

---

## ✅ Summary Table

| Feature                      | Good Practice                            |
|------------------------------|-------------------------------------------|
| Single Delegate              | Simple callback or runtime selection      |
| Multicast Delegate           | Only valid if return type is `void`       |
| Return Type Delegate         | Use `GetInvocationList()` for all results |
| Func<T, Task> Multicast      | ❌ Avoid `+=`; use list of delegates       |
| Await Multiple Async Methods | ✅ Use `Task.WhenAll()`                    |

---

## 🔍 Interview Questions to Practice

1. What is a delegate and how is it different from an interface?
2. Can a delegate reference multiple methods? What happens to the return value?
3. How do you use delegates with async methods?
4. How would you use delegates to implement retry logic or callback?
5. When should you use Func<> vs a custom delegate?
6. How can you invoke and await multiple delegates safely?

---

This covers everything about **delegates** in C# – from basics to async scenarios with real-world use cases. Next, we’ll learn about **Events** and how they build on delegates to support publisher-subscriber patterns.

