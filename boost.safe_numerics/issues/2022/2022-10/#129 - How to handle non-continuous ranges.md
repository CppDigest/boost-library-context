# #129 - How to handle non-continuous ranges? [Open]

> Username: adambadura  
> Created at: 2022-10-08 06:30:42 UTC  
> Updated at: 2022-10-10 09:11:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/129  

Is it possible to somehow include non-continuous ranges? For example. in computation, I'm using an `enum` (converted to its underlying type). The `enum` has a few possible values but the values do not form a continuous range. Of course, I could take the least range covering all possible values. But this would include more values than are truly possible and I'm afraid in some cases of complex computations it could impact the resulting range.

---

## Comment 1

> Username: robertramey  
> Created at: 2022-10-08 06:37:18 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/129#issuecomment-1272244080  

I don’t see how the current library could do this. But if it’s really important to you you might consider investigating how the current libraries implemented. Your “special type of number. “Might be implemented in a similar manner, perhaps borrowing code from the safe library for integers.  
  
Robert Ramey  
www.rrsd.com  
  
> On Oct 7, 2022, at 11:30 PM, Adam Badura ***@***.***> wrote:  
>   
> ﻿  
> Is it possible to somehow include non-continuous ranges? For example. in computation, I'm using an enum (converted to its underlying type). The enum has a few possible values but the values do not form a continuous range. Of course, I could take the least range covering all possible values. But this would include more values than are truly possible and I'm afraid in some cases of complex computations it could impact the resulting range.  
>   
> —  
> Reply to this email directly, view it on GitHub, or unsubscribe.  
> You are receiving this because you are subscribed to this thread.

---

## Comment 2

> Username: adambadura  
> Created at: 2022-10-08 11:43:59 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/129#issuecomment-1272300213  

Well... My first idea was that ranged integer would have to support a list of ranges instead.  
  
One-element list would then be the "special case" corresponding to the current ranged integer. Whereas a list of single values would be the "special case" corresponding to enumeration-like values. (Perhaps, in the future at least, developments in reflection would allow to covert enum value to such list-ranged safe integer within the language itself.)  
  
A list of ranges is needed since further arithmetic could change what was originaly a list of single values into a list of ranges. Connecting overlapping ranges and removing empty ones would have to be handled.  
  
Would it be possible to add something like this to the current implementation of the library?

---

## Comment 3

> Username: adambadura  
> Created at: 2022-10-10 09:11:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/129#issuecomment-1273013424  

I just realized the problem (lack of non-continuous ranges) exists in the library even without considering `enum`-based values. Multiplication introduces this problem already! Consider a simple program ([godbolt.org](https://godbolt.org/z/v7fn5EcGY)):  
```c++  
using delta_safe_t = boost::safe_numerics::safe_signed_range<-1, 1>;  
  
template<int I>  
using const_safe_t = boost::safe_numerics::safe_signed_literal<I>;  
  
int main() {  
  constexpr auto x = const_safe_t<8>{};  
  std::cout << "x = " << safe_format(x) << '\n';  
  constexpr auto y = delta_safe_t{1};  
  std::cout << "y = " << safe_format(y) << '\n';  
  constexpr auto z = y * x;  
  std::cout << "z = " << safe_format(z) << '\n';  
}  
```  
It outputs the following  
```plain  
x = <signed char>[8,8] = 8  
y = <signed char>[-1,1] = 1  
z = <int>[-8,8] = 8  
```  
The problem is visible in the range of `z`. It is `[-8;8]` whereas, in fact, it can be only one of: `-8`, `0`, or `8`.  
  
Now, I cannot readily provide a realistic example where this "drop of precision" leads to subsequent issues but I expect there might be such cases. For example, where divisibility is a required condition.  
  
Then again, now I'm no longer sure this can be reasonably covered. With a precise approach, a multiplication of a ranged integer by a constant would result in a ranged integer with a list of ranges of length equal to the source range length. This could easily exceed compiler limits for templating. Not to mention multiplying two ranged integers.  
  
However, it would be nice to have it somehow covered at least in simpler cases.
