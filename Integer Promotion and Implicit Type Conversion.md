
## 🔹 4. What is _Integer Promotion_ (deep explanation)

Integer promotion is a rule in C++ that automatically **upgrades small integer types** (`char`, `short`, `bool`, etc.) to a larger type (`int` or `unsigned int`) before doing arithmetic or comparisons.

### Why?

Because arithmetic on small types is inefficient and unsafe — CPUs work natively with 32/64-bit registers.

### The rule:

If an expression involves types smaller than `int`:

- They are **promoted to `int`** if all possible values fit in `int`.
    
- Otherwise, promoted to `unsigned int`.
    

### Example 1:

```cpp
uint8_t a = 10, b = 20;
auto c = a + b; // both a, b promoted to int → c is int
```

Even though both are `uint8_t`, the operation happens in `int`.

### Example 2:

```cpp
char c = 'A';
int x = c + 5; // 'A' (65) promoted to int
```

### Example 3 (overflow scenario):

```cpp
uint8_t a = 255;
uint8_t b = 2;
auto c = a + b; // promoted to int → 255 + 2 = 257 (int)
```

If you later assign back to `uint8_t`, it wraps to 1 (mod 256).

---

### Promotion vs Conversion

Promotion only applies to _smaller integer types_.  
If two operands have different ranks (say `int` and `long long`), then _usual arithmetic conversions_ happen — both are converted to the larger type.