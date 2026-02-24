# #41 - Boost atomics on aarch64 produce invalid instructions [Closed]

> Username: tkoecker  
> Created at: 2020-11-19 11:28:15 UTC  
> Updated at: 2020-11-20 11:00:35 UTC  
> Closed at: 2020-11-20 10:58:52 UTC  
> Url: https://github.com/boostorg/atomic/issues/41  

Certain atomic operations result in invalid inline assembly (to large immediate values) to be generated.  
  
Reproducer:  
```c++  
#include <boost/atomic.hpp>  
  
boost::atomic<int> data;  
  
// test atomics for aarch64 with boost 1.74.0  
void test() {  
    // produces a "and w1,w1,64" instruction (valid)  
    data.bitwise_and(64, boost::memory_order_relaxed);   
    // produces a "orr w2,w2,32" instruction (valid)  
    data.bitwise_or(32, boost::memory_order_relaxed);  
    // produces a "eor w2,w2,32" instruction (valid)  
    data.bitwise_xor(32, boost::memory_order_relaxed);  
  
    // produces a "and w1,w1,76" instruction (invalid, valid up to 64)  
    data.bitwise_and(76, boost::memory_order_relaxed);  
    // produces a "orr w2,w2,59" instruction (invalid, valid up to 32)  
    data.bitwise_or(59, boost::memory_order_relaxed);  
    // produces a "eor w2,w2,42" instruction (invalid, valid up to 32)  
    data.bitwise_xor(42, boost::memory_order_relaxed);  
}  
```  
Output:  
```  
/tmp/cc5EHm5U.s: Assembler messages:  
/tmp/cc5EHm5U.s:104: Error: immediate out of range at operand 3 -- `and w2,w2,76'  
/tmp/cc5EHm5U.s:128: Error: immediate out of range at operand 3 -- `orr w2,w2,59'  
/tmp/cc5EHm5U.s:152: Error: immediate out of range at operand 3 -- `eor w2,w2,42'  
Compiler returned: 1  
```  
Example at: https://godbolt.org/z/9cYnq3

---

## Comment 1

> Username: Lastique  
> Created at: 2020-11-20 11:00:35 UTC  
> Url: https://github.com/boostorg/atomic/issues/41#issuecomment-731101105  

Fixed in develop, thanks for the report.
