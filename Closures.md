
# Closures in C#

## 🔍 What is a Closure?

A **closure** is when a function (like a lambda) captures a variable from an outer scope **and holds onto it**, even **after that scope has technically ended**.

Closures allow lambdas and anonymous functions to "remember" the environment in which they were created.

---

## ✅ Common Scenarios Where Closures Are Created

### 1. Loop Variables with Lambdas

```csharp
List<Action> actions = new();
for (int i = 0; i < 3; i++)
{
    actions.Add(() => Console.WriteLine(i)); // Closure! output = 3 3 3
}
```

All lambdas close over the same `i`. The compiler lifts `i` into a hidden class (closure object) to keep it accessible.

### 2. Local Variables in a Method

```csharp
int count = 0;
Action increment = () => { count++; }; // Closure!
increment();
Console.WriteLine(count); // 1
```

### 3. Captured Method Parameters

```csharp
void DoSomething(int start)
{
    Action print = () => Console.WriteLine(start); // Closure!
    print();
}
```

### 4. Closures in LINQ Queries

```csharp
int threshold = 10;
var filtered = myList.Where(x => x > threshold); // Closure!
```

### 5. Closures in Asynchronous Code

```csharp
int value = 42;
await Task.Run(() => Console.WriteLine(value)); // Closure!
```

### 6. Nested Lambdas

```csharp
int a = 5;
Func<Func<int>> nested = () => () => a; // `a` is captured in both!
```

---

## ❗ Beware of Closures in Loops and Asynchronous Code

- **Loop variable capture** can cause bugs.
- **Async closures** can lead to memory leaks if lambdas persist after method completion.

---

## 💡 When Closures Don’t Happen

Closures don’t happen if:

- The lambda doesn’t reference any outer variable.
- You use only constants or literals inside the lambda.

```csharp
Action a = () => Console.WriteLine(5); // No closure needed
```

---

## 🧠 How C# Handles Closures Internally

C# compiler generates a **compiler-generated class** (like `<>c__DisplayClass`) that:

- Stores the captured variables as fields
- Converts your lambda into a method on that class

That’s how variables “stay alive” even after their normal scope ends.

---

## 🔄 Summary Table

| Where Closure Happens | Captured Variable Type  | Why It Happens               |
|------------------------|--------------------------|-------------------------------|
| `for` loops            | `i` from the loop        | Lambda references loop variable |
| Local variables        | Any `int`, `string`, etc | Used inside lambda            |
| Method parameters      | Function args            | Lambda uses parameter         |
| LINQ queries           | Thresholds, filters      | Variables outside query used  |
| Async/await            | Captured state           | Lambda used across threads    |
| Nested lambdas         | Deep-scope variables     | Each lambda accesses the same outer variable |

---

## 🔧 Behind-the-Scenes Example

Compiler might transform:

```csharp
for (int i = 0; i < 3; i++)
{
    actions.Add(() => Console.WriteLine(i));
}
```

Into something like:

```csharp
class Closure {
    public int i;
}

var actions = new List<Action>();
var closure = new Closure();

for (closure.i = 0; closure.i < 3; closure.i++)
{
    actions.Add(() => Console.WriteLine(closure.i));
}
```

All lambdas now reference `closure.i`, not their own copy.

---

If you want to avoid shared state in loops, use:

```csharp
for (int i = 0; i < 3; i++)
{
    int copy = i;
    actions.Add(() => Console.WriteLine(copy));
}
```

Each lambda captures its own `copy`.


# Closure under the hood (sharplab.io)

```csharp
using System;
using System.Collections.Generic;
public class C {

    public void M() {
        
        var actions = new List<Action>();
        for (int i = 0; i < 3; i++)
        {
            actions.Add(() => Console.WriteLine(i));
        }
        foreach (var action in actions)
            action();

        
        int count = 0;
        Action increment = () => { count++; }; // Closure!
        increment();
        Console.WriteLine(count); // 1   
    }

    void DoSomething(int start)
    {
        Action print = () => Console.WriteLine(start); // Closure!
        print();
    }

}
```

internal working 

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Reflection;
using System.Runtime.CompilerServices;
using System.Security;
using System.Security.Permissions;

[assembly: CompilationRelaxations(8)]
[assembly: RuntimeCompatibility(WrapNonExceptionThrows = true)]
[assembly: Debuggable(DebuggableAttribute.DebuggingModes.Default | DebuggableAttribute.DebuggingModes.IgnoreSymbolStoreSequencePoints | DebuggableAttribute.DebuggingModes.EnableEditAndContinue | DebuggableAttribute.DebuggingModes.DisableOptimizations)]
[assembly: SecurityPermission(SecurityAction.RequestMinimum, SkipVerification = true)]
[assembly: AssemblyVersion("0.0.0.0")]
[module: UnverifiableCode]
[module: RefSafetyRules(11)]

public class C
{
    [CompilerGenerated]
    private sealed class <>c__DisplayClass0_0
    {
        public int count;

        internal void <M>b__0()
        {
            count++;
        }
    }


    [CompilerGenerated]
    private sealed class <>c__DisplayClass0_1
    {
        public int i;

        internal void <M>b__1()
        {
            Console.WriteLine(i);
        }
    }


    [CompilerGenerated]
    private sealed class <>c__DisplayClass1_0
    {
        public int start;

        internal void <DoSomething>b__0()
        {
            Console.WriteLine(start);
        }
    }

    public void M()
    {
        <>c__DisplayClass0_0 <>c__DisplayClass0_ = new <>c__DisplayClass0_0();
        List<Action> list = new List<Action>();
        <>c__DisplayClass0_1 <>c__DisplayClass0_2 = new <>c__DisplayClass0_1();
        <>c__DisplayClass0_2.i = 0;
        while (<>c__DisplayClass0_2.i < 3)
        {
            list.Add(new Action(<>c__DisplayClass0_2.<M>b__1));
            <>c__DisplayClass0_2.i++;
        }
        List<Action>.Enumerator enumerator = list.GetEnumerator();
        try
        {
            while (enumerator.MoveNext())
            {
                Action current = enumerator.Current;
                current();
            }
        }
        finally
        {
            ((IDisposable)enumerator).Dispose();
        }
        <>c__DisplayClass0_.count = 0;
        Action action = new Action(<>c__DisplayClass0_.<M>b__0);
        action();
        Console.WriteLine(<>c__DisplayClass0_.count);
    }

    private void DoSomething(int start)
    {
        <>c__DisplayClass1_0 <>c__DisplayClass1_ = new <>c__DisplayClass1_0();
        <>c__DisplayClass1_.start = start;
        Action action = new Action(<>c__DisplayClass1_.<DoSomething>b__0);
        action();
    }
}

```
