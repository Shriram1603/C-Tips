
# 🧠 Why Don't Integers Support `Infinity` or `NaN`?

## ✅ 1. Integer Type System is Discrete
- Integers are whole numbers: `... -2, -1, 0, 1, 2, ...`
- There are no values *between* integers and no representation of overflow or division errors like `∞` or `NaN`.

In contrast:
- Floating-point types (`float`, `double`) follow **IEEE 754**, which defines representations for:
  - `+∞`, `-∞`
  - `NaN` (Not a Number)
  - Subnormal numbers
  - `-0`, `+0`

## ✅ 2. Overflow/Divide-by-Zero in Integers = Logical Error
- For integers, division by zero isn't mathematically meaningful.
- Allowing something like `int.Infinity` would cause **undefined behavior** in integer algorithms (e.g., in loops, indexes, etc.).

## ✅ 3. Performance and Simplicity
- Integers are used in **low-level**, **high-performance**, and **bitwise operations**.
- Supporting `Infinity` or `NaN` would mean every integer would need **extra metadata**, making it slower and larger in memory.

So C# and .NET designers opted to **throw an exception** instead — it's a clearer and safer design for the developer.

---

# 🧾 Cheat Sheet: Float/Double Edge Case Behavior

| Operation                  | Result        | Explanation                          |
|---------------------------|---------------|--------------------------------------|
| `1 / 0f`                  | `+Infinity`   | Positive float divide by zero        |
| `-1 / 0f`                 | `-Infinity`   | Negative float divide by zero        |
| `0f / 0f`                 | `NaN`         | Undefined result                     |
| `Math.Sqrt(-1.0)`         | `NaN`         | Not a real number                    |
| `float.NaN == float.NaN`  | `False`       | NaN is **never equal** to NaN        |
| `float.IsNaN(value)`      | `True`/`False`| Detects NaN                          |
| `float.PositiveInfinity`  | `∞`           | Constant defined in IEEE 754         |
| `float.NegativeInfinity`  | `-∞`          | Constant defined in IEEE 754         |
| `1 / 0`                   | ❌ Exception   | Integer divide-by-zero not allowed   |
| `int.MaxValue + 1`        | Overflow      | Wraps around or throws if checked    |

---

# 🔧 Tips for Dealing with Floats

- Use `float.IsInfinity(x)` and `float.IsNaN(x)` to check before using.
- Don’t compare floats directly. Use a tolerance:
```csharp
if (Math.Abs(a - b) < 0.0001f) { /* Equal */ }
```
