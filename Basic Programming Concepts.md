# **Topic 3: Programming Concepts (Java / C / C++)**

_(Highest weight – logic + concepts, MCQ-oriented)_

---

## **Term 1: Program Control**

### **Iteration (Loops)**

1. **Purpose**: Repeat a block of code.
    
2. **Types**: for, while, do–while.
    
3. **Entry-controlled**: for, while.
    
4. **Exit-controlled**: do–while (executes at least once).
    
5. **Infinite loop**: Condition never becomes false.
    
6. **break**: Exits loop immediately.
    
7. **continue**: Skips current iteration.
    
8. **Loop variable scope**: for-loop variable often block-scoped.
    
9. **MCQ trap**: do–while runs once even if condition false.
    
10. **Time complexity** often tied to loop nesting.
    

---

### **Recursion**

1. **Function calls itself**.
    
2. **Base case**: Mandatory to stop recursion.
    
3. **Recursive case**: Calls itself with smaller input.
    
4. **Uses stack memory**.
    
5. **Risk**: Stack overflow.
    
6. **Direct vs indirect recursion**.
    
7. **Tail recursion**: Recursive call is last statement.
    
8. **Slower than iteration** (usually).
    
9. **MCQ favorite**: Missing base case → infinite recursion.
    
10. **Recursion ≠ iteration** (memory usage differs).
    

---

### **Functions**

1. **Reusable block of code**.
    
2. **Return type**: Specifies output type.
    
3. **Parameters vs Arguments**:
    
    - Parameters = definition
        
    - Arguments = call
        
4. **Function prototype** (C/C++).
    
5. **Void function**: No return value.
    
6. **Call by value**:
    
    - Copy passed.
        
7. **Call by reference**:
    
    - Address passed.
        
8. **Function overloading**:
    
    - Same name, different parameters.
        
9. **MCQ trap**: Overloading ≠ overriding.
    
10. **Main function**: Program entry point.
    

---

## **Term 2: Scope of Variables**

1. **Scope**: Region where variable is accessible.
    
2. **Local variable**:
    
    - Declared inside block/function.
        
3. **Global variable**:
    
    - Declared outside all functions.
        
4. **Block scope**:
    
    - Inside `{}`.
        
5. **Lifetime**:
    
    - How long variable exists.
        
6. **Static variable**:
    
    - Retains value between calls.
        
7. **Global vs Local conflict**:
    
    - Local has higher priority.
        
8. **Java**:
    
    - No true global variables.
        
9. **MCQ favorite**:
    
    - Scope ≠ lifetime.
        
10. **Uninitialized local variable**:
    

- Error in Java, garbage in C.
    

---

## **Term 3: Binding of Variables & Functions**

1. **Binding**: Association between name and value.
    
2. **Static binding**:
    
    - Compile time.
        
3. **Dynamic binding**:
    
    - Runtime.
        
4. **Early binding**:
    
    - Function call resolved at compile time.
        
5. **Late binding**:
    
    - Resolved at runtime.
        
6. **Virtual functions** (C++) → dynamic binding.
    
7. **Method overriding**:
    
    - Uses dynamic binding.
        
8. **Overloading**:
    
    - Static binding.
        
9. **MCQ favorite**:
    
    - Binding ≠ scope.
        
10. **Java default**:
    

- Methods are dynamically bound (unless static/final).
    

---

## **Term 4: Parameter Passing**

1. **Call by Value**:
    
    - Copy passed.
        
2. **Call by Reference**:
    
    - Address/reference passed.
        
3. **C**:
    
    - Only call by value.
        
4. **C++**:
    
    - Supports both.
        
5. **Java**:
    
    - Always call by value (object reference passed by value).
        
6. **Effect of reference**:
    
    - Changes reflect outside function.
        
7. **Arrays**:
    
    - Act like reference in C/C++.
        
8. **MCQ trap**:
    
    - Java is NOT call by reference.
        
9. **Used for**:
    
    - Efficiency, multiple outputs.
        
10. **Pointer passing**:
    

- Common in C.
    

---

## **Term 5: Functional Programming**

1. **Focus**:
    
    - Functions as first-class citizens.
        
2. **No shared state**.
    
3. **No side effects**.
    
4. **Immutability**:
    
    - Data not changed.
        
5. **Pure functions**:
    
    - Same input → same output.
        
6. **Recursion preferred** over loops.
    
7. **Higher-order functions**:
    
    - Functions as arguments.
        
8. **Examples**:
    
    - map, filter, reduce.
        
9. **Java**:
    
    - Lambda expressions.
        
10. **MCQ trap**:
    

- Functional ≠ Object-oriented.
    

---

## **Term 6: Logic Programming**

1. **Based on**:
    
    - Facts and rules.
        
2. **Uses**:
    
    - Predicate logic.
        
3. **Program = knowledge base**.
    
4. **Query-based execution**.
    
5. **No control flow** like loops.
    
6. **Example language**:
    
    - Prolog.
        
7. **Focus**:
    
    - What is true, not how.
        
8. **Backtracking used**.
    
9. **Declarative paradigm**.
    
10. **MCQ favorite**:
    

- Logic programming ≠ procedural.
    

---

## **Term 7: OOPS Concepts**

1. **Encapsulation**:
    
    - Data + methods together.
        
2. **Abstraction**:
    
    - Hiding implementation details.
        
3. **Inheritance**:
    
    - Acquire properties of parent.
        
4. **Polymorphism**:
    
    - Many forms.
        
5. **Class**:
    
    - Blueprint.
        
6. **Object**:
    
    - Instance of class.
        
7. **Access specifiers**:
    
    - public, private, protected.
        
8. **MCQ favorite**:
    
    - Abstraction ≠ encapsulation.
        
9. **Supports code reuse**.
    
10. **Java**:
    

- Everything inside a class.
    

---

## **Term 8: Inheritance**

1. **IS-A relationship**.
    
2. **Base class / Derived class**.
    
3. **Single inheritance**:
    
    - Java supports.
        
4. **Multiple inheritance**:
    
    - Not via classes in Java.
        
5. **Method overriding**:
    
    - Runtime polymorphism.
        
6. **super keyword**:
    
    - Access parent members.
        
7. **private members**:
    
    - Not inherited.
        
8. **Constructors**:
    
    - Not inherited.
        
9. **Diamond problem**:
    
    - Reason Java avoids multiple inheritance.
        
10. **MCQ trap**:
    

- Overloading ≠ overriding.
    

---

## **Term 9: Class and Object**

1. **Class**:
    
    - Logical entity.
        
2. **Object**:
    
    - Physical entity.
        
3. **Multiple objects** from one class.
    
4. **Data members + methods**.
    
5. **Memory allocation**:
    
    - Objects at runtime.
        
6. **new keyword** (Java).
    
7. **Access via dot operator**.
    
8. **Each object has own copy of data**.
    
9. **Class loaded once**.
    
10. **MCQ favorite**:
    

- Object = instance of class.
    

---

## **Term 10: Constructors**

1. **Special function**.
    
2. **Same name as class**.
    
3. **No return type**.
    
4. **Called automatically**.
    
5. **Default constructor**:
    
    - No arguments.
        
6. **Parameterized constructor**.
    
7. **Constructor overloading**:
    
    - Allowed.
        
8. **Used to initialize objects**.
    
9. **Not inherited**.
    
10. **MCQ trap**:
    

- Constructor ≠ method.
    

---

## **Term 11: Exception Handling**

1. **Handles runtime errors**.
    
2. **try–catch block**.
    
3. **finally**:
    
    - Always executes.
        
4. **throw**:
    
    - Explicitly throw exception.
        
5. **throws**:
    
    - Declare exception.
        
6. **Checked exceptions**:
    
    - Compile-time (Java).
        
7. **Unchecked exceptions**:
    
    - Runtime.
        
8. **Exception vs Error**:
    
    - Error not recoverable.
        
9. **Multiple catch blocks allowed**.
    
10. **MCQ favorite**:
    

- finally executes even if exception occurs.