# #277 - Increment of certain multi-limb negative values produces incorrect results [Closed]

> Username: gordondow  
> Created at: 2020-12-15 18:24:06 UTC  
> Updated at: 2020-12-30 18:54:19 UTC  
> Closed at: 2020-12-30 18:54:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/277  

```  
using boost::multiprecision::cpp_int;  
  
// Construct the negative value -0x10000000000000001 (15 zeroes between the 1's, or -(2**128)+1  
cpp_int x = 1;  
x <<= 128;  
x++;  
x = -x;  
  
std::cout << x << '\n'; // writes '-340282366920938463463374607431768211457' (a very negative value)  
++x;  
std::cout << x << '\n'; // writes '340282366920938463463374607431768211456' (a very positive value)  
```  
The problem is in the function `eval_increment` in [add.hpp](https://github.com/boostorg/multiprecision/blob/4700c047951256bd6f7d250f366ee2e41667904b/include/boost/multiprecision/cpp_int/add.hpp#L266). The intent is to flip the sign bit if the increment goes from -1 to 0, but that code neglects to consider the possibility of multiple limbs. I was able to resolve it by changing that line to:  
```  
if (!result.limbs()[0] && result.size() == 1)  
```  
An alternative fix, with more symmetry to the `eval_decrement` implementation (but arguably less efficient), would be:  
```  
if (!result.sign() && (result.limbs()[0] < cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1>::max_limb_value))  
   ++result.limbs()[0];  
else if (result.sign() && result.limbs()[0] > 1)  
   --result.limbs()[0];  
else  
   eval_add(result, one);  
```  
I took a look at `eval_decrement` and I _don't_ see a similar problem there.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-26 16:03:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/277#issuecomment-751369734  

Confirmed.
