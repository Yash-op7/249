### **Runtime vs Static Polymorphism**

**Short answer:**

| Feature       | Static Polymorphism                      | Runtime Polymorphism            |
| ------------- | ---------------------------------------- | ------------------------------- |
| Binding time  | Compile time                             | Runtime                         |
| Achieved via  | Method overloading, operator overloading | Method overriding (inheritance) |
| Decision made | By compiler                              | By JVM / runtime system         |
| Flexibility   | Less flexible                            | More flexible                   |
| Performance   | Faster                                   | Slightly slower                 |
Even though the **inheritance structure is known at compile time**, the **actual object type used at a call site may not be known until runtime**.  
That’s why the decision is **dynamic**, not static.

## Explaination
### What the compiler knows

At compile time, the compiler knows:

- Which classes inherit from which
    
- Which methods are overridden
    
- The **type of the reference variable**
    

Example:

`Animal a;`

The compiler only knows:

> `a` is an **Animal reference**

---

### What the compiler does NOT know

The compiler does **not** know:

- Which **actual object** `a` will point to at runtime
    

Example:

`a = new Dog();   // could also be new Cat()`

That decision may depend on:

- User input
- Program logic
- Configuration
- Runtime conditions
```java
Animal a = new Dog();
a.sound();
```

## Why this is NOT static polymorphism

Static polymorphism means:

- Method call is fixed **at compile time**
    
- Based only on reference type and method signature
    

Runtime polymorphism means:

- Method call depends on **actual object type**
    
- Decided **while the program is running**
    

Even though inheritance is known, **the binding is delayed**.