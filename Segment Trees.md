main idea: **Efficiently answer queries on a range of an array while also supporting updates.**
# Goal

Given an array of integers, support both:

```
Range Query
Point Update
```

in

```
O(log N)
```
example: range sum (l,r) queries with point/range updates

## Common Problem Patterns
### 1. Range Sum

```
sum(l,r)
```

### 2. Range Minimum

```
min(l,r)
```

### 3. Range Maximum

```
max(l,r)
```

### 4. Range GCD

```
gcd(l,r)
```

### 5. Range XOR

```
xor(l,r)
```

### 6. Count of something

```
count evens
count odds
count positives
```

### 7. Custom Information

Store:

```
(max value, frequency)
```

or

```
(sum, minimum)
```

etc.


## Pattern Recognition
1. Online queries; Cannot preprocess all answers.
2. Need associative operation → Because segment trees combine answers from children :-
	1. sum  
	2. min  
	3. max  
	4. gcd  
	5. xor
## visualization
                [0..7]

        [0..3]          [4..7]

    [0..1] [2..3]   [4..5] [6..7]

   ...
   Each node stores answer for its segment.
# 5. Memory Size

For array size N → Allocate this:

```cpp
vector<int> seg(4*N);
```

4N is always safe.

# 6. Operations

Three fundamental operations:

### Build

Construct tree: `O(N)`

Pseudocode for sum:
```cpp
build(node, start, end)

if start == end:
    tree[node] = arr[start]
    return

mid = (start + end)/2

build(left child)
build(right child)

tree[node] =
    tree[left child] + tree[right child]
```

---

# Query

Answer range: `O(log N)`

At each node there are 3 cases.
#### Case 1: Complete Overlap → Return node value.
Entire node lies inside query.

#### Case 2: No Overlap → Return neutral element.

For sum: 0 is the neutral element

#### Case 3: Partial Overlap → Need to go both sides.

Combine results.

# Query Pseudocode

```cpp
query(node,start,end,l,r)

if no overlap:
    return 0

if complete overlap:
    return tree[node]

mid

left =
    query(left child)

right =
    query(right child)

return left + right
```

---

### Update

Modify element: `O(log N)`

Suppose:

```
arr[3] = 10
```

Only one root-to-leaf path changes. (in normal mode, there is an extension of segment tree where range is updated)

We update:

```go
leaf
parent
grandparent
...
root
```
## Update Pseudocode

```cpp
update(node,start,end,idx,val)

if start==end:
    tree[node]=val
    return

mid

go left or right

recompute current node
```

# Full pseudocode
## Build
```cpp
build(node,l,r)

if l==r:
    tree[node]=arr[l]
    return

mid=(l+r)/2

build(2*node,l,mid)
build(2*node+1,mid+1,r)

tree[node]=
tree[2*node]+tree[2*node+1]
```
## Query
```cpp
query(node,l,r,ql,qr)

if qr<l or r<ql:
    return 0

if ql<=l and r<=qr:
    return tree[node]

mid=(l+r)/2

left=query(...)
right=query(...)

return left+right
```
## Update (point update)
```cpp
update(node,l,r,idx,val)

if l==r:
    tree[node]=val
    return

mid=(l+r)/2

if idx<=mid:
    update(left)
else:
    update(right)

tree[node] = tree[left]+tree[right]
```

## Generic Segment Tree Thinking

#### Sum Tree

```cpp
merge(a,b)=a+b
identity=0
```

---

#### Min Tree

```cpp
merge(a,b)=min(a,b)
identity=INF
```

---

#### Max Tree

```cpp
merge(a,b)=max(a,b)
identity=-INF
```

---

#### GCD Tree

```cpp
merge(a,b)=gcd(a,b)
identity=0

because gcd(x,0)=x
```
## Best CP Implementation
```cpp
class SegmentTree {
    int n;
    vector<long long> tree;

public:
    SegmentTree(vector<int>& arr) {
        n = arr.size();
        tree.resize(4 * n);
        build(1, 0, n - 1, arr);
    }

    void build(int node, int l, int r,
               vector<int>& arr) {

        if (l == r) {
            tree[node] = arr[l];
            return;
        }

        int mid = (l + r) / 2;

        build(node * 2, l, mid, arr);
        build(node * 2 + 1, mid + 1, r, arr);

        tree[node] =
            tree[node * 2] +
            tree[node * 2 + 1];
    }

    long long query(int node,
                    int l,
                    int r,
                    int ql,
                    int qr) {

        if (qr < l || r < ql)
            return 0;

        if (ql <= l && r <= qr)
            return tree[node];

        int mid = (l + r) / 2;

        return query(node * 2,
                     l,
                     mid,
                     ql,
                     qr)
             +
               query(node * 2 + 1,
                     mid + 1,
                     r,
                     ql,
                     qr);
    }

    void update(int node,
                int l,
                int r,
                int idx,
                int val) {

        if (l == r) {
            tree[node] = val;
            return;
        }

        int mid = (l + r) / 2;

        if (idx <= mid)
            update(node * 2,
                   l,
                   mid,
                   idx,
                   val);
        else
            update(node * 2 + 1,
                   mid + 1,
                   r,
                   idx,
                   val);

        tree[node] =
            tree[node * 2] +
            tree[node * 2 + 1];
    }

    long long query(int l, int r) {
        return query(1, 0, n - 1, l, r);
    }

    void update(int idx, int val) {
        update(1, 0, n - 1, idx, val);
    }
};
```

### Usage
```cpp
vector<int> arr = {2,5,1,4,9};

SegmentTree st(arr);

cout << st.query(1,3);

st.update(2,10);

cout << st.query(1,3);
```