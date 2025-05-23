
# 🎌 Flagged Enums in C#

## 🔧 What Is a Flagged Enum?

A flagged enum uses the `[Flags]` attribute and assigns **powers of two** to each member so they can be **combined using bitwise OR (`|`)** and checked using **bitwise AND (`&`)**.

```csharp
[Flags]
enum FileAccess {
    None = 0,
    Read = 1,         // 0001
    Write = 2,        // 0010
    Execute = 4,      // 0100
    ReadWrite = Read | Write // 0011
}
```

## 🧠 How It Works Under the Hood

- Each value is a bit position: 1, 2, 4, 8, 16, etc.
- You can combine them using `|` (OR), and test with `&` (AND).

```csharp
FileAccess permissions = FileAccess.Read | FileAccess.Write;
Console.WriteLine(permissions); // Output: Read, Write
```

## 📋 Checking Flags

```csharp
if ((permissions & FileAccess.Read) == FileAccess.Read)
{
    Console.WriteLine("Read permission granted");
}
```

## 🎯 Use Cases

- Access permissions (Read, Write, Execute)
- UI options (Bold, Italic, Underline)
- Game states (IsFlying, IsInvisible)
- Feature toggles

## ⚠️ Important Notes

- `[Flags]` is optional but helps `ToString()` return meaningful output.
- Use powers of two (1, 2, 4, 8…) for each value.
- Use compound values only intentionally.

## 🔍 Enum Utility Methods

```csharp
permissions.HasFlag(FileAccess.Read)
```

Note: `HasFlag()` is slower and causes boxing in older versions.

---

# 🎮 Real-World Example: Game Character Status

```csharp
[Flags]
enum StatusEffects
{
    None       = 0,
    Poisoned   = 1 << 0, // 00001
    Stunned    = 1 << 1, // 00010
    Invisible  = 1 << 2, // 00100
    Flying     = 1 << 3, // 01000
    Frozen     = 1 << 4  // 10000
}
```

### Character Class

```csharp
class Character
{
    public string Name { get; set; }
    public StatusEffects CurrentStatus { get; set; }

    public void AddEffect(StatusEffects effect)
    {
        CurrentStatus |= effect;
    }

    public void RemoveEffect(StatusEffects effect)
    {
        CurrentStatus &= ~effect;
    }

    public bool HasEffect(StatusEffects effect)
    {
        return (CurrentStatus & effect) == effect;
    }

    public override string ToString()
    {
        return $"{Name} has status: {CurrentStatus}";
    }
}
```

### Example Usage

```csharp
var hero = new Character { Name = "Arthas" };

hero.AddEffect(StatusEffects.Poisoned);
hero.AddEffect(StatusEffects.Flying);

Console.WriteLine(hero); // Arthas has status: Poisoned, Flying

if (hero.HasEffect(StatusEffects.Flying))
    Console.WriteLine($"{hero.Name} is flying!");

hero.RemoveEffect(StatusEffects.Poisoned);

Console.WriteLine(hero); // Arthas has status: Flying
```

## ✅ Benefits

- Compact representation
- Readable logic for adding/removing/checking effects
- Efficient and scalable
