# Stack vs Heap

## Stack

- **Stack memory** is sequential, fixed-size, and managed with simple pointer arithmetic.
- It is very fast because there is **no need for searching** values, as it uses **step-by-step execution**.
- Lives in the **RAM** (memory).
- Values in the stack are short live.

### Analogy
The stack is like a **vertical pile of plates**. Adding or removing a plate (variable) is fast — just move the "top" pointer.

### Characteristics
- **Allocations** are just pointer increments.
- **No search is required** because it’s a step-by-step execution.
- **Deallocations** happen automatically when the function exits.
- No need to track **lifetimes**.

---

## Heap

- **Heap memory** is **dynamic**, with **no size limitations**, and managed by the **garbage collector (GC)**, which involves more overhead.
- All the **objects in the heap** are arranged **randomly**, so it takes some time to **find** an object. This makes it **slower** compared to the stack.
- Values in the stack are long live.

### Analogy
The heap is more like a **parking lot**: you can park anywhere, but now you need to keep track of where everything is, what’s in use, and when it can be removed.

### Characteristics
- **Allocations** are references.
- **Search is required** because it’s stores the data ramdomly in memory.
- **Deallocations** happen by GC.
- Track **lifetimes**.
