
## 🔹 3. What is `size_t`

`size_t` is an **unsigned integer type** used to represent _sizes_ and _indices_.  
Defined in `<cstddef>` and `<cstdint>`.

Formally:

> `size_t` is the unsigned integer type returned by the `sizeof` operator.

### ✅ Example

```cpp
sizeof(int) → returns a size_t

for (size_t i = 0; i < v.size(); i++) ...
```
### Why unsigned?

Because sizes and array indices can never be negative.

### Why use `size_t` instead of `int`?

- It’s guaranteed to be large enough to represent the maximum possible size of any object in memory.
    
- It matches standard library interfaces (like `std::vector::size()`).
    

Example:

```cpp
std::vector<int> v;
auto n = v.size();  // type of n = size_t
```

### On 64-bit systems:

- `size_t` is 8 bytes (0 to 2⁶⁴−1).
    
- `int` is 4 bytes (−2³¹ to 2³¹−1).
    

So if you mix them (e.g., `for (int i = 0; i < v.size(); i++)`), the compiler warns about signed/unsigned comparison.