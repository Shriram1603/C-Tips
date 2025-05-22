# Understanding Nullable Types in C#

This document is a complete reference of a deep-dive discussion into how `Nullable<T>` works in C#, the motivations behind nullability for value types, and how null is handled for both value and reference types.

---

## 📌 1. Default Values in C#

### Value Types (like `int`, `bool`, `DateTime`)
- Stored on the stack.
- Always have a value.
- Default is usually `0`, `false`, or `DateTime.MinValue`.

```csharp
int a; // default is 0
```

### Nullable Value Types (`int?`, `bool?`, etc.)
- Backed by `Nullable<T>` struct.
- Can hold a value OR null.
- Default is `null`.

```csharp
int? b; // default is null
```

### Reference Types (like `string`, arrays, class objects)
- Stored on the heap.
- Can store `null` by design.
- Default is `null`.

```csharp
string s; // default is null
```

---

## 📦 2. How `Nullable<T>` Works

### Definition (simplified):

```csharp
public struct Nullable<T> where T : struct
{
    private bool hasValue;
    private T value;

    public bool HasValue => hasValue;
    public T Value => hasValue ? value : throw new InvalidOperationException();
}
```

### Key Points:
- `HasValue` indicates whether `Value` contains valid data.
- If `HasValue == false`, accessing `Value` throws an exception.
- When you write `int? x = null;`, you're actually saying:
  > `Nullable<int> x = new Nullable<int>();`

---

## 🔍 3. What is `null`?

- For reference types: `null` = no object (pointer is empty).
- For value types: `null` = no value present (`HasValue == false`).
- It’s a **semantic placeholder** meaning “missing”, “not set”, or “unknown”.

```csharp
int? x = null; // HasValue = false, no valid int stored
```

### `x == null`
This evaluates to `true` if `HasValue == false`.

---

## 🔗 4. Nullable vs Reference Types

| Feature           | Reference Types      | Value Types            |
|-------------------|----------------------|-------------------------|
| Can be null       | Yes                  | No (unless nullable)    |
| Default value     | null                 | 0 / false / MinValue    |
| Null-check        | Direct               | Via HasValue            |
| Null-safe access  | `?.` works           | `?.` works when nullable|

### Why `string` is a class, not a struct?
- Strings are reference types so they can easily support null, immutability, and interning.

---

## 🔐 5. Null-Conditional Operator `?.`

```csharp
string s = null;
Console.WriteLine(s?.Length); // Outputs nothing, no exception
```

- `s?.Length` returns `null` instead of throwing.
- It **safely navigates** through potential `null` references.
- If `s != null`, returns `s.Length` (as `int?`).
- If `s == null`, returns `null`.

---

## ❓ 6. Why Null for Value Types?

To represent cases like:
- Missing database data
- Optional parameters
- Unset form fields
- Tri-state flags (`true`, `false`, `null`)

### Examples:
```csharp
int? age = null;            // user didn't enter age
bool? isSubscribed = null;  // tri-state: yes/no/not answered
```

---

## 💡 7. Nullable Utilities & Operators

### `default(int?)`
Returns `null`.

```csharp
int? x = default(int?); // x is null
```

### Null-Coalescing `??`
```csharp
int? x = null;
int y = x ?? 10; // y = 10
```

### Null-Coalescing Assignment `??=`
```csharp
string name = null;
name ??= "Guest";
```

### `GetValueOrDefault()`
```csharp
int? x = null;
Console.WriteLine(x.GetValueOrDefault()); // 0
```

### Nullable in Expressions
```csharp
int? a = 10, b = null;
Console.WriteLine(a > b); // false: null is unknown, so comparison cannot be done
var result = a + b; // null: any artithmetic operation on null is null
```

---

## ✅ Summary Table

| Feature                  | Purpose                                     |
|--------------------------|---------------------------------------------|
| `int?`                  | Nullable int (backed by Nullable<T>)         |
| `??`                    | Fallback/default if null                     |
| `??=`                   | Assign default only if null                  |
| `?.`                    | Null-safe member access                      |
| `GetValueOrDefault()`   | Gets value or 0                              |
| `default(int?)`         | Gets null                                    |

---

> Nullable types provide a clean and consistent way to represent missing or optional data in value types — a crucial feature when working with databases, forms, APIs, and real-world logic.
