# 📘 **Alias, Require, Import — What They Are & When To Use Them**

These three keywords help you **use other modules** in your code more conveniently

# 🔹 1. `alias` — shorten long module names

If a module has a long or nested name:

`alias Very.Long.Module.Name`

Now you can write:

`Name.function()`

Instead of:

`Very.Long.Module.Name.function()`

You are NOT importing functions — you’re only creating a shorter alias.

### Example:

```elixir
alias Foo.Bar.Baz
Baz.run()
```

# 🔹 3. `import` — bring functions into your current namespace

This actually _copies_ functions/macros into your current scope.

Example:

```exs
import List
flatten([1, [2,3]])   # allowed without List.flatten
```

Be careful: importing too much can make code ambiguous

---

# 🔹 4. Selective Import (Best Practice)

You can import only specific functions:

`import Enum, only: [map: 2, reduce: 3]`

Or exclude:

`import Enum, except: [map: 2]`

---
# 🔹 5. What Elixir Developers Actually Do

Real-world usage patterns:

|Keyword|When used|
|---|---|
|`alias`|Very common — shorten module names|
|`require`|Rare — only when using macros|
|`import`|Used carefully — often avoided unless clarity improves|

For your assignment, you will **use almost none** of these.  
Everything can be called directly with module names.

# 📘 **Module Attributes — Simple, Practical Explanation**

Module attributes in Elixir serve **three purposes**:

1️⃣ **Constants** (most common use)  
2️⃣ **Compile-time configuration**  
3️⃣ **Metadata for tools (docs, testing, etc.)**

Let’s cover each cleanly.

---

# 🔹 1. **Module Attributes as Constants**

This is the use you’ll see 90% of the time.

```exs
defmodule Circle do
  @pi 3.14159
  def area(r), do: r * r * @pi
end
```

- `@pi` is evaluated **at compile-time**, not runtime.
    
- It cannot change.
    
- Works exactly like constants in other languages.
    

This is the way you will use attributes the most — especially for config values in your assignment.

Example for assignment:

`@tick_rate 10`

---

# 🔹 2. **Module Attributes for Compile-Time Behavior**

Some modules react to attributes during compilation.

Examples:

- `@behaviour`
    
- `@derive`
    
- `@before_compile`
    

You don’t need any of these right now.

Just know:

> Some Elixir libraries read module attributes to change how they compile.

---

# 🔹 3. **Module Attributes as Metadata**

Used by docs, testing, and tools.

Examples:

`@moduledoc "This module does X..." @doc "Explains what a function does" @test_tag :slow`

You’ve already seen attributes like:

- `@moduledoc`
    
- `@doc`
    

These are purely metadata, not values.

---

# 🔹 How Attributes Work Internally

Important rule:

> An attribute is assigned at **compile-time**, not at runtime.

You cannot do:

`@x = x + 1   # ❌ Invalid`

You can only define them once per module (unless using accumulator attributes — not needed now).

---

# 🔹 4. Using Module Attributes as Accumulators (Advanced)

Elixir lets you accumulate values:

`Module.register_attribute(__MODULE__, :routes, accumulate: true)  @routes {"/", :home} @routes {"/login", :login}`

This creates a list of routes.

This is used in frameworks (Phoenix Router), but **you don’t need it now**.

---

# ⭐ Summary (the only parts you need today)

Module attributes are:

|Purpose|Example|You need it?|
|---|---|---|
|**Constants**|`@tick_rate 30`|✔ yes|
|Compile-time metadata|`@moduledoc`, `@doc`|✔ trivial|
|Macro/system config|`@derive`, `@behaviour`|❌ not now|
|Accumulators|Phoenix routes|❌ not now|

For your assignment, the only useful form is:

```
defmodule Config do
  @num_fireflies 50
  @off_duration 2.0
end
```

Nothing more complicated.

---
# 🗺️ **MAPS in Elixir**

### 👉 Think of a _map_ as a **dictionary** of key–value pairs.

- Keys can be **any type**: strings, atoms, numbers, etc.
    
- Values can also be anything.
    
- Order does **not** matter.
    

### 📦 Creating a map

```exs
person = %{
  name: "Alice",
  age: 30
}
```

### 📌 Accessing values

```exs
person.name     # => "Alice"   (works only for atom keys)
person[:age]    # => 30
```

### ➕ Updating a map

```exs
new_person = %{person | age: 31}
```


> This works only if the key already exists.

Or add new keys:

```
person = Map.put(person, :city, "London")
```

---

# 📘 **Structs — The Cleanest Possible Explanation**

Structs are **just maps with a fixed set of keys**, defined inside a module.

They give you:

- Default values
- Compile-time key checking
- Clear data shape
- Pattern matching
- Still behaves like a map
    

They **do NOT** give:

- Methods
    
- Classes
    
- OOP behavior
    

---

# 🔹 1. **Defining a Struct**

```
defmodule User do
  defstruct name: "", age: 0
end
```


This automatically creates a struct:

`%User{name: "John", age: 0}`

---

# 🔹 2. **Creating a Struct**



```
user = %User{name: "Alice", age: 30}     # Creating a struct
user.name   # => "Alice"   # Accessing fields
user = %{user | age: 31}   # Updating a struct
```

Structs are **maps**, so:

`is_map(user)   # true`

But a map **cannot** pretend to be a struct:

`%User{} = %{name: "Yash"}        # ❌ doesn't match`

---

# 🔹 3. **Structs Enforce Keys**

Fail:

`%User{nonexistent: 123}   # ❌ Compile error`

This is the main benefit vs maps:

- You can’t misspell keys
    
- You can’t add random new keys
    
- You know the shape beforehand
    

---

# 🔹 4. **Accessing Fields**

Exactly like maps:

`user.name user.age`

Structs do not have methods; modules only define functions.

---

# 🔹 5. **Updating a Struct**

Structs are immutable — so you create a new one:

`new_user = %{user | age: 24}`

Important:

- You can only update existing keys
    
- Cannot add new keys
    

---

# 🔹 6. **Pattern Matching on Structs**

You’ll use this a lot later:

`%User{name: name} = user`

This extracts `name`.

---

# 🔥 Where Structs Matter for You

You can use structs to define the shape of data for your fireflies, example:

```
defstruct state: :off, time_left: 0.0, left: nil
```

Struct improves clarity but is **not required** for the assignment.

---

# ✨ Summary (in one breath)

> A struct is a map defined inside a module, with a fixed set of fields and optional defaults.  
> It ensures correctness and supports pattern matching.

### 🔑 Simple rule:

- Use **maps** for loose, flexible data.
    
- Use **structs** when the data represents a specific “thing” with a known shape.

Sidenote: The `fn … end` multi-clause syntax ONLY works for **anonymous functions**.

**named function** clauses use multiple `def` heads
they do not use **->**


---

# 📘 **Recursion — The Elixir Way of Looping**

Elixir has **no `for` or `while` loops** like C++ or JS.  
Instead, Elixir uses **recursion + pattern matching** to repeat work.

---

# 🔹 1. Recursive Functions Replace Loops

Example: sum a list.

`defmodule Math do   def sum([]), do: 0   def sum([head | tail]), do: head + sum(tail) end`

This is the classic recursive style:

- Base case
    
- Recursive case
    

---

# 🔹 2. Pattern Matching Drives Recursion

List structure is:

`[head | tail]`

Where:

- `head` = first element
    
- `tail` = rest of the list
    

So each recursive call uses a **smaller list** until the base case hits `[]`.

---

# 🔹 3. Tail Recursion (Important Optimization)

BEAM optimizes **tail recursive** functions:

> A function is tail recursive if the last expression _is the recursive call itself_.

Example (NOT tail recursive):

`def sum([h | t]) do   h + sum(t) end`

Example (tail recursive):

`def sum(list), do: sum(list, 0)  defp sum([], acc), do: acc defp sum([h | t], acc), do: sum(t, h + acc)`

Here:

- Accumulator (`acc`) keeps intermediate results
    
- Recursive call is the last operation → tail optimized
    

This is how you write efficient loops in Elixir.

# 🎯 Final takeaway (SUPER simple version)

➡️ **Normal recursion:**  
“I’ll finish computing later. Let me remember this step.”

➡️ **Tail recursion:**  
“I already did my work. Someone else will finish.”

➡️ **BEAM optimization:**  
“Great, I'll throw away old steps and reuse memory!”

---

# 🔹 4. Why Tail Recursion Matters?

Because:

- Normal recursion consumes a new stack frame each call
    
- Tail recursion **reuses the same stack frame**
    
- You can loop millions of times safely
    

Elixir makes this extremely efficient.

---

# 🔹 5. Recursion is Everywhere in Elixir

You’ll use recursion when:

- Looping manually
    
- Handling messages in a process
    
- Waiting for events in a `receive` loop
    

Example `receive` loop:

`def loop(state) do   receive do     {:update, new} -> loop(new)   end end`

This is a **tail recursive infinite loop** — essential for concurrency.

You'll write something just like this in your assignment.

---

# ✨ Key takeaway

> Elixir replaces loops with **recursive functions**; efficient code uses **tail recursion with accumulators**.

---