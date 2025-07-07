# 🧠 Mastering Events, EventHandler, and EventArgs in C\#

This guide walks through real-world use cases of C# events, EventHandler, and EventArgs via progressive problem-solving. Each section includes the problem statement, hands-on code, conceptual explanation, feedback, and a tightly coupled comparison.

---

## ✅ Q1: Basic Event Subscription

### ❓ Problem Statement

Create an `Alarm` class that triggers an event. Let two other classes, `Logger` and `SMS`, subscribe to it and respond.

### ✅ Code

```csharp
public class Logger
{
    public void LogAlarmData()
    {
        Console.WriteLine("Logger message Alarm triggered ");
    }
}

public class SMS
{
    public void SendSMS()
    {
        Console.WriteLine("Sending Alarm message ");
    }
}

public class Alarm
{
    public event Action AlarmTriggered;

    public void TriggerAlarm()
    {
        Console.WriteLine("Alarm Triggered !!");
        AlarmTriggered?.Invoke();
    }
}

static void Main()
{
    var alarm = new Alarm();
    var logger = new Logger();
    var sms = new SMS();

    alarm.AlarmTriggered += logger.LogAlarmData;
    alarm.AlarmTriggered += sms.SendSMS;

    alarm.TriggerAlarm();
}
```

### 🧠 Key Learning

- `event` prevents reassignment of the delegate from outside the class.
- Multiple methods can subscribe to the same event.

---

## ✅ Q2: Introducing EventHandler and Sender

### ❓ Problem Statement

Modify the system to identify **which object** triggered the event.

### ✅ Code

```csharp
public class Alarm
{
    public event EventHandler AlarmTriggered;
    public string Name { get; }

    public Alarm(string name)
    {
        Name = name;
    }

    public void TriggerAlarm()
    {
        Console.WriteLine("Alarm Triggered !!");
        AlarmTriggered?.Invoke(this, EventArgs.Empty);
    }

    public override string ToString() => Name;
}

public class Logger
{
    public void LogAlarmData(object sender, EventArgs e)
    {
        Console.WriteLine($"Log Alarm triggered by device: {sender}");
    }
}
```

### 🧠 Key Learning

- `sender` parameter tells you **which object** raised the event.
- Use `ToString()` override or casting to access additional properties.

---

## ✅ Q3: Passing Data via EventArgs

### ❓ Problem Statement

Enhance your event to include rich data such as message and timestamp.

### ✅ Code

```csharp
public class AlarmEventArgs : EventArgs
{
    public string Message { get; }
    public DateTimeOffset Time { get; }

    public AlarmEventArgs(string message)
    {
        Message = message;
        Time = DateTimeOffset.Now;
    }
}

public class Alarm
{
    public event EventHandler<AlarmEventArgs> AlarmTriggered;
    public string Name { get; }

    public Alarm(string name)
    {
        Name = name;
    }

    public void TriggerAlarm(AlarmEventArgs e)
    {
        Console.WriteLine("Alarm Triggered !!");
        AlarmTriggered?.Invoke(this, e);
    }

    public override string ToString() => Name;
}

public class Logger
{
    public void LogAlarmData(object sender, AlarmEventArgs e)
    {
        Console.WriteLine($"Log Alarm triggered by device: {sender}, Info: {e.Message}, Time: {e.Time}");
    }
}
```

### 🧠 Key Learning

- `EventHandler<T>` enables passing structured event data.
- Allows extensible and rich communication between publisher and subscribers.

---

## ✅ Q4: Full Event-Driven Smart Home System

### ❓ Problem Statement

Design a smart home system with devices like `Thermostat`, `Alarm`, `DoorSensor`, each triggering events to a shared `NotificationCenter`.

### ✅ Code

```csharp
public class SmartDeviceEventArgs : EventArgs
{
    public string Message { get; }
    public DateTimeOffset TimeStamp { get; }
    public Severity Severity { get; }

    public SmartDeviceEventArgs(string message, Severity severity)
    {
        Message = message;
        Severity = severity;
        TimeStamp = DateTimeOffset.Now;
    }
}

public enum Severity { Low, Medium, High }

public class NotificationCenter
{
    public void PushNotification(object sender, SmartDeviceEventArgs e)
    {
        Console.WriteLine($"[{e.Severity}] [{e.TimeStamp}] {sender}: {e.Message}");
    }
}

public class Alarm
{
    public event EventHandler<SmartDeviceEventArgs> AlarmTriggered;
    public string Name { get; }

    public Alarm(string name) => Name = name;

    public void TriggerAlarm(SmartDeviceEventArgs e) => AlarmTriggered?.Invoke(this, e);

    public override string ToString() => Name;
}

// Similar for Thermostat, DoorSensor...
```

### 🧠 Key Learning

- Centralized event handling with `NotificationCenter` decouples device logic from notification logic.
- Reusable, extensible, scalable design.

---

## 🔄 Without Events – Tightly Coupled Version

### ❌ Problem

Every device directly depends on `NotificationCenter`. No subscribers, no flexibility.

### 🧱 Code

```csharp
public class Alarm
{
    private readonly NotificationCenter _center;
    public string Name { get; }

    public Alarm(string name, NotificationCenter center)
    {
        Name = name;
        _center = center;
    }

    public void TriggerAlarm(SmartDeviceEventArgs e)
    {
        _center.PushNotification(Name, e);
    }
}

public class NotificationCenter
{
    public void PushNotification(string deviceName, SmartDeviceEventArgs e)
    {
        Console.WriteLine($"[{e.Severity}] [{e.TimeStamp}] {deviceName}: {e.Message}");
    }
}
```

### 🔎 Why This Is Worse

| Problem            | Description                                   |
| ------------------ | --------------------------------------------- |
| 🔗 Tight Coupling  | Devices know and depend on NotificationCenter |
| 🧱 Hard to Extend  | Can’t add Logger or Email easily              |
| 🔁 Low Reusability | Devices cannot work with different handlers   |

---

## 🎓 Final Reflection

> Using `event`, `EventHandler`, and `EventArgs` creates a highly modular, reusable, and decoupled system. Devices can publish events freely while letting external handlers decide how to respond. This leads to cleaner, more scalable designs, especially in UI frameworks, IoT systems, and distributed applications.



