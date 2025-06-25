# Events in C# - Complete Guide

## 📌 What is an Event?

An **event** in C# is a type-safe way to allow a **class to notify subscribers** when something happens. It is built on top of delegates and follows the **publisher-subscriber pattern**.

```csharp
public event Action SomethingHappened;
```

- **Publisher** raises the event.
- **Subscribers** respond to the event by attaching handler methods.

The `event` keyword ensures that only the class that declares the event can raise (invoke) it, while other classes can only subscribe (`+=`) or unsubscribe (`-=`).

---

## ✅ Why Use Events?

- Decouple classes (loose coupling)
- Implement pub-sub pattern
- Notify external systems of internal state changes
- Standard pattern in .NET UI, data, async, etc.

---

## 🔧 Basic Event Syntax

### Step 1: Declare the event
```csharp
public event Action AlarmTriggered;
```

### Step 2: Raise (invoke) the event
```csharp
AlarmTriggered?.Invoke();
```

### Step 3: Subscribe to the event
```csharp
alarm.AlarmTriggered += OnAlarmTriggered;
```

### Step 4: Define the handler
```csharp
void OnAlarmTriggered() => Console.WriteLine("Handled!");
```

---

## 👨‍💻 Hands-On: Basic Alarm Event

```csharp
namespace EventsPractice
{
    public class Alarm
    {
        public event Action AlarmTriggered;

        public void TriggerAlarm()
        {
            Console.WriteLine("Alarm is being triggered!");
            AlarmTriggered?.Invoke();
        }

        internal class Program
        {
            static void Main(string[] args)
            {
                var alarm = new Alarm();

                // First subscription
                alarm.AlarmTriggered += OnAlarmTriggered;

                // Overwriting directly (possible here because Program is nested in Alarm)
                alarm.AlarmTriggered = OnAlarmTriggered2; // ⚠️ Overwrites previous handler

                alarm.TriggerAlarm();
            }

            public static void OnAlarmTriggered()
            {
                Console.WriteLine("Alarm has been triggered!");
            }

            public static void OnAlarmTriggered2()
            {
                Console.WriteLine("Alarm has been triggered2!");
            }
        }
    }
}
```

### 🧠 Important Note:

Even though events prevent `=` assignment from outside, **nested classes** (like `Program` inside `Alarm`) have access to the private members of the outer class and **can overwrite** event handlers.

---

## ⚙️ Why Use EventHandler and EventArgs?

When you want to pass additional data with the event (like timestamp, message, old value, etc.), you use the standard .NET pattern:

```csharp
public event EventHandler<CustomEventArgs> SomethingHappened;
```

This lets you send:
- The sender (who raised the event)
- Event-specific data

---

## 🔧 Hands-On: Alarm with EventHandler and EventArgs

### Step 1: Define the `EventArgs` subclass
```csharp
public class AlarmEventArgs : EventArgs
{
    public string Message { get; }
    public AlarmEventArgs(string message) => Message = message;
}
```

### Step 2: Define the event using `EventHandler<T>`
```csharp
public class SmartAlarm
{
    public event EventHandler<AlarmEventArgs> AlarmTriggered;

    public void Trigger(string message)
    {
        Console.WriteLine("Smart Alarm Triggered");
        AlarmTriggered?.Invoke(this, new AlarmEventArgs(message));
    }
}
```

### Step 3: Subscribe and handle the event
```csharp
public class Program
{
    public static void Main()
    {
        var alarm = new SmartAlarm();
        alarm.AlarmTriggered += HandleSmartAlarm;

        alarm.Trigger("Motion detected at 2:00 AM");
    }

    static void HandleSmartAlarm(object sender, AlarmEventArgs e)
    {
        Console.WriteLine($"Smart Alarm handled: {e.Message}");
    }
}
```

---

## 📦 Real-World Example: Product Price Change

### Custom EventArgs
```csharp
public class PriceChangedEventArgs : EventArgs
{
    public decimal OldPrice { get; }
    public decimal NewPrice { get; }

    public PriceChangedEventArgs(decimal oldPrice, decimal newPrice)
    {
        OldPrice = oldPrice;
        NewPrice = newPrice;
    }
}
```

### Event declaration and raise
```csharp
public class Product
{
    public decimal Price { get; private set; }
    public event EventHandler<PriceChangedEventArgs> PriceChanged;

    public void UpdatePrice(decimal newPrice)
    {
        if (Price != newPrice)
        {
            var oldPrice = Price;
            Price = newPrice;
            PriceChanged?.Invoke(this, new PriceChangedEventArgs(oldPrice, newPrice));
        }
    }
}
```

### Subscriber
```csharp
Product product = new Product();
product.PriceChanged += (sender, args) =>
{
    Console.WriteLine($"Price changed from {args.OldPrice} to {args.NewPrice}");
};

product.UpdatePrice(120);
```

---

## 🧠 Benefits of EventHandler/EventArgs Pattern

| Feature           | Explanation                                                      |
|------------------|------------------------------------------------------------------|
| `sender` param   | Who raised the event (can filter or log based on origin)         |
| `EventArgs`      | Custom data attached to the event                                |
| Type-safety      | Strongly typed and consistent across .NET ecosystem              |
| Reusability      | Standard pattern used in WinForms, WPF, ASP.NET, and other libs  |

---

## ✅ Summary Table

| Concept                      | Explanation                                          |
|-----------------------------|------------------------------------------------------|
| `event` keyword              | Restricts invocation of delegate from outside class |
| `Action` event               | Basic events with no data                           |
| `EventHandler`              | Standard event signature                            |
| `EventHandler<T>`           | Allows sending strongly-typed data                  |
| `EventArgs` subclass        | Container for additional event info                 |
| Nested class access         | Can overwrite event handlers (use with caution)     |

---

## 🔍 Interview Practice Questions

1. What is the difference between a delegate and an event?
2. Why do we use the `event` keyword?
3. What does `EventHandler<T>` do that `Action` doesn't?
4. What are the benefits of using `EventArgs`?
5. What happens when you use `=` instead of `+=` on an event?
6. How can a class notify multiple subscribers with additional data?

---

This concludes a deep dive into **Events in C#**, including how to define, raise, subscribe, and safely pass data using `EventHandler<T>`. Next, we’ll move on to **Plan 3: Func, Action, Predicate in C# with Real-World Use Cases**.

