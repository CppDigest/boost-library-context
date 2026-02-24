# #209 - [Feature request] boost::unordered_flat_map variant with key and value stored separately [Open]

> Username: holenno1  
> Created at: 2023-08-28 16:00:09 UTC  
> Updated at: 2023-09-08 16:51:30 UTC  
> Url: https://github.com/boostorg/unordered/issues/209  

Hello!  
  
It would be great if we could have another variant of `boost::unordered_flat_map` where the keys and values are stored in separate arrays - "split" storage.  
The main motivation for this is to minimise memory wastage due to padding.  
  
eg: key=`uint64_t`, value=`float` would mean 4 bytes of padding per key-value pair.  
  
Obviously this will be a further divergence from `std::unordered_map`'s interface.  
The main difference being that iterators will return `std::pair<const Key&, Value&>` instead of `std::pair<const Key, Value>&`.  
  
However, we believe this will provide a tonne of value to fans of `boost::unordered_flat_map`.  
  
Thanks!

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-08-31 22:48:57 UTC  
> Url: https://github.com/boostorg/unordered/issues/209#issuecomment-1701880176  

> However, we believe this will provide a tonne of value to fans of `boost::unordered_flat_map`.  
  
Hmm, do you have any concrete use-cases or data for this?  
  
We'd like to consider the idea but before dedicating the resources, we'd like to get a gauge on the effectiveness of including such a map.

---

## Comment 2

> Username: ktprime  
> Created at: 2023-09-02 06:38:02 UTC  
> Url: https://github.com/boostorg/unordered/issues/209#issuecomment-1703730390  

@holenno1 This design needs at least three arrays. Even it can save some memory in  use-cases, but it's performance is not as good as you think so in most cases.

---

## Comment 3

> Username: holenno1  
> Created at: 2023-09-07 14:44:41 UTC  
> Updated at: 2023-09-07 14:49:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/209#issuecomment-1710282595  

Hi, sorry was busy all week.  
  
I've collected some data,  
  
--------------  
  
A bit of context:  
  
We have an in-house open-addressing hash map implementation that is heavily based on LLVM's `llvm::DenseMap`.  
That means  
- Power-of-2 buffer sizes  
- Quadratic probing (triangular number sequence)  
- 75% maximum load factor  
- 12.5% maximum tombstone ratio  
  
`llvm::DenseMap` relies on 2 predefined sentinel values to indicate if a slot is "empty" or "erased/tombstone" .  
Our implementation instead stores an extra bitset, where each slot in the buffer is represented by 2 bits for the aforementioned tags.  
  
In addition, we have another hash map variant that stores the keys and values in separate buffers - "split storage".  
As mentioned in our feature request, this is a primarily a memory optimisation.  
  
--------------  
  
Here's a memory usage experiment:  
- `key_type = const A*` (`alignof(A) = 1`)  
- `mapped_type = uint32_t`  
- number of entries = 100,000  
  
A real world usage scenario for this setup is object-to-id mapping - we have a large number of heap allocated objects, and we want to assign an ID to each of them.  
  
In fact, 100,000 entries is a little on the low side.  
We typically deal with 100s of millions or low-billions of entries, which is the typical size of a large netlist or graph problem.  
  
Here are the results:  
```  
boost::unordered_flat_map                    : 4,194,304 Bytes  
llvm::DenseMap derivative                    : 8,388,608 Bytes  
llvm::DenseMap derivative w/ split key-value : 6,291,456 Bytes  
```  
*Those numbers were measured by querying `/proc/self/stat - Virtual memory size in bytes`  
  
The "split storage" variant sees a 25% reduction in memory usage compared to the "pair storage" variant.  
  
--------------  
  
Let's look at runtime.  
For this, I extended the [Unordered benchmark suite](https://github.com/boostorg/boost_unordered_benchmarks/tree/boost_unordered_flat_map)  
  
Yes @ktprime you are right, the "split storage" variant will have a performance disadvantage for insertion and erasure workloads.  
  
However, "split storage" does have a slight edge when it comes to unsuccessful lookups, as we don't have to visit the value buffer.  
  
![image](https://github.com/boostorg/unordered/assets/116388074/23bab45b-a716-4a2a-adfc-9489db579c88)  
*Those numbers were measured with an isolated Xeon W-2155 (Skylake) server with gcc 12.2, boost 1.81 at -O3

---

## Comment 4

> Username: martinus  
> Created at: 2023-09-08 09:26:08 UTC  
> Url: https://github.com/boostorg/unordered/issues/209#issuecomment-1711361319  

Couldn't you just create a custom key type (say with `std::array<std::byte, 8>`, or two `uint32_t` types) so that the alignment doesn't cause padding? then access the data with `memcpy`. I don't think it would cause much of a slowdown on most systems.

---

## Comment 5

> Username: holenno1  
> Created at: 2023-09-08 16:48:10 UTC  
> Updated at: 2023-09-08 16:51:30 UTC  
> Url: https://github.com/boostorg/unordered/issues/209#issuecomment-1711960518  

~I think you mean use `unordered_flat_set` (not map) + type punning + `const_cast`?~  
EDIT: Ok I see you meant  
```  
struct key {  std::byte raw[8]; };  
struct value {  std::byte raw[4]; };  
  
using my_map = boost::unordered_flat_map<key, value, key_hash, key_eq>;  
```  
  
We can certainly do that, but I think the spirit of the feature request is so we don't have to actually do that :).
