
# Understanding the Discard Operator `_` in C# (Async Context)

## What is `_ = SomeAsyncMethod();`?

In C#, the discard operator `_` is used to **explicitly ignore** a returned value. When used with async methods:

```csharp
_ = LoadGameHistoryAsync();
```

You're telling the compiler:

> "Yes, I know this returns a `Task`, but I don't care about the result or waiting for it to finish."

---

## Why Use `_ =` Instead of Just Calling the Method?

If you just call an async method like this:

```csharp
LoadGameHistoryAsync();
```

The compiler gives a warning:

> "Because this call is not awaited, execution of the current method continues before the call is completed."

Using `_ =`:

- **Suppresses this warning**
- Shows **intentional** fire-and-forget behavior

---

## Use Cases

✅ Use `_ =` when:

- You **don't need** to wait for the task to complete
- The method **handles exceptions internally**

---

## Example

```csharp
private string _selectedSortColumn = "PlayedAt";
public string SelectedSortColumn
{
    get => _selectedSortColumn;
    set
    {
        Set(ref _selectedSortColumn, value);
        _ = LoadGameHistoryAsync(); // Fire-and-forget
    }
}
```

---

## Safer Fire-and-Forget Pattern

To catch exceptions in fire-and-forget tasks, wrap them:

```csharp
_ = Task.Run(async () =>
{
    try
    {
        await LoadGameHistoryAsync();
    }
    catch (Exception ex)
    {
        // Handle or log the exception
    }
});
```

---

## Summary Table

| Expression              | Meaning                                                  |
| ----------------------- | -------------------------------------------------------- |
| `await MethodAsync();`  | Waits for task to complete, exceptions bubble up         |
| `MethodAsync();`        | Fire-and-forget, **compiler warning**                    |
| `_ = MethodAsync();`    | Fire-and-forget, **suppresses warning**, no result usage |
| `Task.Run(...)` wrapper | Fire-and-forget with **internal exception handling**     |

---

## Final Notes

- The discard `_` is just a **convention**, not a keyword
- It makes your intent to **ignore a return value** explicit
- Use it responsibly, especially with `async` methods, to avoid hidden bugs
