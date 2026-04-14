1. In recursive parsing problems (for example [LC394](https://leetcode.com/problems/decode-string/?envType=problem-list-v2&envId=wnjhzswi))
	1. **Passing index by reference** *: This is the key trick in recursive parsing problems.*
##  String important point
![[Pasted image 20260210194604.png]] 
-> Never use '+' for string concatenation inside loops (Never use `+` when the string is growing repeatedly)
-> and if you do return str1 + str2, then:
![[Pasted image 20260210195901.png]]
-> Also always try to do str.reserve(size) whenever possible
3. When parsing numerics in strings, try to avoid stoi and just do this:
   ![[Pasted image 20260210200009.png]]
4. In "normal" (i.e. not inherently recursive, like not dp, graph, but string parse) questions if you are thinking of a recursive solution, try to think of the iterative solution using a stack more as that is what interviewers want.
5. Also try to use the inbuilt `bool isdigit(char c)` function: `isdigit(s[idx])`
6. essential C++ utilities:
```cpp
// ===== Character checks (<cctype>) =====
isdigit(c)                         // check if digit '0'–'9'  ⭐
isalpha(c)                         // check if alphabet letter  ⭐
islower(c)                         // check if lowercase letter  ⭐
isupper(c)                         // check if uppercase letter  ⭐
isalnum(c)                         // check if alphanumeric  ⭐
isspace(c)                         // check if whitespace (space, tab, newline)  ⭐
tolower(c)                         // convert char to lowercase  ⭐
toupper(c)                         // convert char to uppercase  ⭐

// ===== String utilities (<string>) =====
s.size()                           // length of string (O(1))
s.empty()                          // check if string is empty
s.clear()                          // remove all characters
s.substr(pos, len)                // get substring
s.find(str)                        // find substring, returns index or npos
s.rfind(str)                       // find substring from right
s.push_back(c)                    // append single character
s.pop_back()                      // remove last character
s.append(str)                     // append string in-place
s += str                          // append string in-place (same as append)
stoi(s)                           // string to int
stoll(s)                          // string to long long
to_string(num)                    // number to string

// ===== Algorithms (<algorithm>) =====
sort(v.begin(), v.end())           // sort ascending
sort(v.begin(), v.end(), greater<int>()) // sort descending
reverse(v.begin(), v.end())        // reverse container
find(v.begin(), v.end(), x)        // linear search
binary_search(v.begin(), v.end(), x) // binary search (sorted)
lower_bound(v.begin(), v.end(), x) // first element >= x  ⭐
upper_bound(v.begin(), v.end(), x) // first element > x  ⭐
count(v.begin(), v.end(), x)       // count occurrences of x  ⭐
min(a, b)                          // minimum of two values  ⭐
max(a, b)                          // maximum of two values  ⭐
*min_element(v.begin(), v.end())   // minimum element in container  ⭐
*max_element(v.begin(), v.end())   // maximum element in container  ⭐
next_permutation(v.begin(), v.end()) // next lexicographic permutation
prev_permutation(v.begin(), v.end()) // previous lexicographic permutation
rotate(v.begin(), v.begin()+k, v.end()) // rotate left by k

// ===== Containers =====
v.push_back(x)                     // insert at end (vector)
v.pop_back()                       // remove last element
v.back()                           // access last element
v.front()                          // access first element
st.push(x)                         // push to stack
st.pop()                           // pop from stack
st.top()                           // top element of stack
q.push(x)                          // push to queue
q.pop()                            // pop from queue
q.front()                          // front of queue
dq.push_back(x)                    // push back (deque)
dq.push_front(x)                   // push front (deque)

// ===== Hashing =====
mp[key]++                          // increment frequency
mp.count(key)                     // check if key exists  ⭐
mp.erase(key)                     // erase key from map/set  ⭐

// ===== Priority Queue (Heap) =====
priority_queue<int> pq;            // max heap  ⭐
priority_queue<int, vector<int>, greater<int>> minpq; // min heap  ⭐

// ===== Math (<cmath>) =====
abs(x)                             // absolute value
sqrt(x)                            // square root
ceil(x)                            // round up
floor(x)                           // round down
pow(a, b)                          // a raised to power b (double)

// ===== Bit manipulation =====
__builtin_popcount(x)              // count set bits (1s)  ⭐
__builtin_clz(x)                   // count leading zeros  ⭐
__builtin_ctz(x)                   // count trailing zeros  ⭐

// ===== Utilities =====
assert(condition)                  // crash if condition is false  ⭐
INT_MAX                            // maximum int value
INT_MIN                            // minimum int value
LLONG_MAX                          // maximum long long value  ⭐
```
7. Timestamp trick to clear a visited array in O(1):
### Timestamp trick is basically:

> "simulate clearing an array in O(1) time"

This trick shows up everywhere:

- distinct elements in subarrays
    
- visited arrays in BFS/DFS repeated many times
    
- repeated queries on arrays
8. ⭐ Important clean Gravity pattern: https://leetcode.com/problems/candy-crush/?envType=problem-list-v2&envId=w0kqjbb7 (see code below)
9. ```cpp
       void condense(auto &board) {
        for(int col = 0; col < m; col++) {
        int write = n - 1;
        for(int row = n - 1; row >= 0; row--) {
            if(board[row][col] != 0)
                board[write--][col] = board[row][col];
        }
        while(write >= 0)
            board[write--][col] = 0;
        }
    }
   ```
   10. Fermat's Little Theorem -> for division of large numbers we need modular inverse
   ![[Pasted image 20260301140015.png]]![[Pasted image 20260301140104.png]]
 ![[Pasted image 20260301140136.png]]
 ![[Pasted image 20260301140151.png]]  ![[Pasted image 20260301140315.png]] https://leetcode.com/problems/sum-of-k-digit-numbers-in-a-range/description/
 ![[Pasted image 20260301140249.png]] ![[Pasted image 20260301140451.png]]
 
 # Side note: ⤵️
 
![[Pasted image 20260301140548.png]] 
 
 ![[Pasted image 20260301140504.png]] ![[Pasted image 20260301140533.png]] 11. 🚀 Very Important: Computing nCr Efficiently
 ![[Pasted image 20260301142552.png]]
 ![[Pasted image 20260301142616.png]]
 8. Try to find the minimal state representation
 9. [1980. Find Unique Binary String](https://leetcode.com/problems/find-unique-binary-string/) brilliant idea: **Construct a string that differs from the i-th string at position i.** ⭐
10. Matrix rotation via **transpose + reverse** is a standard trick used in many problems.
11. When calculate space complexity dont forget about recursion depth ❗❗
12. Writing clear code in interviews comprises of:
	1. Not using sentinel values, such as `ans = -1 or ans = 1`, instead use constants such as `LLONG_MAX` or define your own constants
	2. Use clear and better variable names
	3. Dont use auto keyword in function parameter, since it reduces readability
	4. dont use inline ternaries, it requires more mental effort to understand the code flow