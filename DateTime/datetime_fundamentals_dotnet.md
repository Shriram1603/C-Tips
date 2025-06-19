
# Working with Dates and Times in .NET – Complete Reference

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Date and Time Ambiguity](#date-and-time-ambiguity)
3. [ISO 8601 Format](#iso-8601-format)
4. [DateTime in .NET](#datetime-in-net)
5. [TimeZoneInfo and Conversions](#timezoneinfo-and-conversions)
6. [UTC vs Local Time](#utc-vs-local-time)
7. [DateTimeOffset](#datetimeoffset)
8. [Parsing and Formatting](#parsing-and-formatting)
9. [DateOnly and TimeOnly](#dateonly-and-timeonly)
10. [TimeProvider in .NET 8](#timeprovider-in-net-8)
11. [NodaTime - Alternative Library](#nodatime---alternative-library)
12. [Best Practices Summary](#best-practices-summary)
13. [Hands-on](#Hands-on-Practice-go-through-to-see-DST-in-Action)

---

## Introduction

Working with dates and times is inherently complex due to cultural formats, time zones, and daylight savings. .NET provides a suite of types to help manage this: `DateTime`, `DateTimeOffset`, `TimeSpan`, `DateOnly`, and `TimeOnly`.

---

## Date and Time Ambiguity

One of the biggest issues:

- `"10/06/2024"` → 10th June or 6th October?
- Ambiguity arises from cultural formats.
- Solution: **Always store and communicate using ISO 8601.**

---

## ISO 8601 Format

A standardized format for date and time:
```
2024-06-10T14:00:00+05:30
```
- `T` is the time delimiter.
- Final part is the UTC offset (`+05:30`), or `Z` (Zulu = UTC).

**Example:**
```csharp
DateTimeOffset now = DateTimeOffset.UtcNow;
Console.WriteLine(now.ToString("O")); // Outputs: 2024-06-10T14:00:00.0000000+00:00
```

---

## DateTime in .NET

- Stores a date and time, but **no time zone offset**.
- Can be `Unspecified`, `Local`, or `Utc` via `DateTimeKind`.

**Pitfall:** Using `DateTime.Now` stores local time without offset info.

**Example:**
```csharp
DateTime dt = DateTime.Now;
Console.WriteLine($"Local time: {dt}, Kind: {dt.Kind}");
```

---

## TimeZoneInfo and Conversions

Convert between time zones:
```csharp
var local = DateTime.Now;
var tz = TimeZoneInfo.FindSystemTimeZoneById("E. Australia Standard Time");
var sydney = TimeZoneInfo.ConvertTime(local, TimeZoneInfo.Local, tz);
```

List all time zones matching your current offset:
```csharp
var offset = DateTimeOffset.Now.Offset;
var zones = TimeZoneInfo.GetSystemTimeZones()
    .Where(z => z.GetUtcOffset(DateTime.UtcNow) == offset);
```

---

## UTC vs Local Time

**Prefer UTC for storage and transmission.**

```csharp
DateTime utcNow = DateTime.UtcNow;
DateTime local = utcNow.ToLocalTime();
```

For offset-aware time:
```csharp
DateTimeOffset utc = DateTimeOffset.UtcNow;
DateTimeOffset local = utc.ToLocalTime();
```

---

## DateTimeOffset

- Stores both **date/time and offset**.
- Preferred for API data and database storage.

**Example:**
```csharp
DateTimeOffset dto = DateTimeOffset.Now;
Console.WriteLine(dto);  // 2024-06-10T15:30:00+01:00
```

---

## Parsing and Formatting

**Parse with explicit format:**
```csharp
var format = "MM/dd/yyyy hh:mm:ss tt";
var culture = CultureInfo.InvariantCulture;
DateTime dt = DateTime.ParseExact("09/10/2024 06:30:00 PM", format, culture);
```

**Parse with offset:**
```csharp
var dateWithOffset = "2024-07-01T18:00:00-05:00";
var parsed = DateTimeOffset.Parse(dateWithOffset);
```

**Convert to ISO 8601 string:**
```csharp
Console.WriteLine(parsed.ToString("O"));
```

---

## DateOnly and TimeOnly

.NET 6+ features:

```csharp
var dateOnly = new DateOnly(2024, 6, 10);
var timeOnly = new TimeOnly(14, 30);
Console.WriteLine(dateOnly);  // 06/10/2024
Console.WriteLine(timeOnly);  // 14:30
```

Convert from `DateTime`:
```csharp
var dt = DateTime.Now;
var dateOnly = DateOnly.FromDateTime(dt);
var timeOnly = TimeOnly.FromDateTime(dt);
```

---

## TimeProvider in .NET 8

New abstraction for testable time access.

```csharp
public class MyService {
    private readonly TimeProvider _timeProvider;
    public MyService(TimeProvider timeProvider) => _timeProvider = timeProvider;

    public DateTimeOffset GetNow() => _timeProvider.GetUtcNow();
}
```

Use `FakeTimeProvider` for testing:
```csharp
var fake = new FakeTimeProvider();
fake.SetUtcNow(new DateTimeOffset(2024, 6, 10, 12, 0, 0, TimeSpan.Zero));
```

---

## NodaTime – Alternative Library

NodaTime provides precise and unambiguous date/time handling.

```csharp
var now = SystemClock.Instance.GetCurrentInstant();
var zone = DateTimeZoneProviders.Tzdb["Australia/Sydney"];
var zoned = now.InZone(zone);
Console.WriteLine(zoned); // 2024-06-10T12:30:00 Australia/Sydney (+10)
```

Advantages:
- Stores exact time zone, not just offset.
- Great for global and calendar applications.

---

## Best Practices Summary

✅ Use `DateTimeOffset.UtcNow` for current timestamps.  
✅ Store timestamps in UTC.  
✅ Prefer `DateTimeOffset` over `DateTime`.  
✅ Use `ToString("O")` for ISO 8601.  
✅ Use `ParseExact` to avoid format ambiguity.  
✅ Use `DateOnly`/`TimeOnly` when full DateTime is not needed.  
✅ Use `TimeProvider` for testability.  
✅ Use `NodaTime` for advanced scenarios.

---

*End of Module 1 – Fundamentals of Dates and Times in .NET*


## Hands-on Practice go through to see DST in Action

```csharp
namespace DateTime_Hands_on
{
    internal class Program
    {
        static void Main(string[] args)
        {
            // DateTimeOffset will show your local offset. DateTime won’t.
            Console.WriteLine("DateTime.Now:           " + DateTime.Now);
            Console.WriteLine("DateTimeOffset.Now:     " + DateTimeOffset.Now);
            Console.WriteLine("DateTime.UtcNow:        " + DateTime.UtcNow);
            Console.WriteLine("DateTimeOffset.UtcNow:  " + DateTimeOffset.UtcNow);

            // Parsing offset
            var isoString = "2023-10-01T12:00:00+05:30";
            var parsed = DateTimeOffset.Parse(isoString);
            Console.WriteLine("Parsed : " + parsed);
            Console.WriteLine("ISO 8601: " + parsed.ToString("O"));


            // Converting between Time Zones
            var utcNow = DateTimeOffset.UtcNow;
            var londonTimeZone = TimeZoneInfo.FindSystemTimeZoneById("GMT Standard Time");
            var londonTime = TimeZoneInfo.ConvertTime(utcNow, londonTimeZone);
            Console.WriteLine("Current UTC Time: " + utcNow);
            Console.WriteLine("London Time: " + londonTime);

            // DST in Action

            var dstDate = new DateTimeOffset(2025, 7, 1, 12, 0, 0, TimeSpan.Zero); // Summer
            var standardDate = new DateTimeOffset(2025, 12, 1, 12, 0, 0, TimeSpan.Zero); // Winter

            var london = TimeZoneInfo.FindSystemTimeZoneById("GMT Standard Time");

            Console.WriteLine($"Offset on July 1:  {london.GetUtcOffset(dstDate)}");       // +01:00
            Console.WriteLine($"Offset on Dec 1:   {london.GetUtcOffset(standardDate)}"); // +00:00

            var india = TimeZoneInfo.FindSystemTimeZoneById("India Standard Time");

            Console.WriteLine($"Offset on July 1 in India:  {india.GetUtcOffset(dstDate)}");       // +05:30
            var currentIndianTime = TimeZoneInfo.ConvertTime(utcNow, india);
            var currentLondonTime = TimeZoneInfo.ConvertTime(utcNow, london);
            Console.WriteLine($"Current Indian Time: {currentIndianTime}");
            Console.WriteLine($"Current London Time: {currentLondonTime}");


        }
    }
}

```
