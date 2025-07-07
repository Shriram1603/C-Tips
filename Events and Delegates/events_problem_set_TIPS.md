# 📘 Events and EventHandler Practice Problems in C#

These progressive exercises are designed to help you master **events**, **EventHandler**, and **custom EventArgs** in C#. You’ll build up from basic usage to building a fully decoupled, event-driven smart home simulation.

---

## 🧩 Question 1: Basic Event Subscription

### 📝 Problem Statement
Create a class `Alarm` that can raise an event `AlarmTriggered`. Then create two other classes: `Logger` and `SMS`. These should subscribe to the event and react accordingly when the alarm is triggered.

### 🔍 Goals
- Understand how to declare and raise events using `event Action`.
- Learn how to subscribe multiple handlers to a single event.

---

## 🧩 Question 2: Who Triggered the Event?

### 📝 Problem Statement
Modify your previous system to indicate **which alarm object** triggered the event. You may have multiple `Alarm` objects, and you should be able to distinguish which one triggered the event.

### 🔍 Goals
- Use `EventHandler` instead of `Action`.
- Pass `sender` object and `EventArgs.Empty` when invoking the event.
- Override `ToString()` in your `Alarm` class for meaningful output.

---

## 🧩 Question 3: Send Data with the Event

### 📝 Problem Statement
Extend your event system to send **additional data** when the alarm is triggered, such as a message and a timestamp.

### 🔍 Goals
- Create a custom class `AlarmEventArgs` that inherits from `EventArgs`.
- Replace `EventHandler` with `EventHandler<AlarmEventArgs>`.
- Use that to pass message and timestamp to the subscribers.

---

## 🧩 Question 4: Build a Real Smart Home Event System

### 📝 Problem Statement
Design a smart home simulation with multiple device types: `Thermostat`, `Alarm`, and `DoorSensor`. Each should raise an event using a **shared event args type** called `SmartDeviceEventArgs`. All events should be handled by a shared subscriber: `NotificationCenter`.

### 🔍 Goals
- Create an enum `Severity { Low, Medium, High }`.
- Create `SmartDeviceEventArgs` with properties `Message`, `TimeStamp`, and `Severity`.
- Use `EventHandler<SmartDeviceEventArgs>` for each device.
- Use one central subscriber (`NotificationCenter`) to log all events.

---

## 🧱 Bonus Challenge: Why Events Matter

Try rewriting your solution from Q4 **without using events**. Pass the `NotificationCenter` directly into each device and call it manually from the trigger method.

Then reflect:
- What’s the downside of this approach?
- How is it harder to extend or reuse?

---

These questions simulate real-world use cases like UI actions, IoT devices, or service triggers — where you need to **publish changes** and let others **subscribe and respond**. Good luck, and happy learning! 🚀

