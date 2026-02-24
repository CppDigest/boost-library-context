# #76 - Strange int-to-string attribute conversions [Closed]

> Username: akrzemi1  
> Created at: 2024-01-25 21:26:33 UTC  
> Updated at: 2024-02-04 19:39:55 UTC  
> Closed at: 2024-01-28 22:45:40 UTC  
> Url: https://github.com/boostorg/parser/issues/76  

I suspect that it is a bug that the following example compiles:  
  
```c++  
int main()  
{  
  std::string result;  
  auto b = bp::parse("3", bp::int_, bp::ws, result);   
  //                                        ^~~~ storing an int into string  
}  
```  
Shouldn't some attribute conversion check detect it? This even runs, and returns `true`.  
Full example: https://godbolt.org/z/bMv3er6eY

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-26 01:36:28 UTC  
> Url: https://github.com/boostorg/parser/issues/76#issuecomment-1911270150  

Nope.  It's just the way `std::string` works, unfortunately.  This is fine:  
  
```c++  
std::string str;  
str = 3; // Implicit int->char conversion.  
```  
  
That's why your example is well-formed.  You could also have used `bp::double_` with similar results.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-26 19:43:51 UTC  
> Url: https://github.com/boostorg/parser/issues/76#issuecomment-1912599255  

I would encourage you to special-case the int-to-string, double-to-string conversion.  
Converting anything but `char` to string is an unintended behavior (maybe except for literal `0`).  
https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p2037r1.html  
Even assignment from `char` is fishy: it is like assigning an element to the container of elements.  
The only reason the committee decided not to fix it is the backwards compatibility. Clang-tidy warns about it.  
  
You will only do your users a favour by disallowing these (even at the cost of special-casing broken `std::string`. You have the potential to prevent user bugs, without a risk of breaking any valid user code.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-27 03:28:04 UTC  
> Url: https://github.com/boostorg/parser/issues/76#issuecomment-1912945200  

Ha! I forgot you wrote that paper.  I was aware of it.  I voted for it IIRC.  Yeah, that's fair though.  This *is* super surprising.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2024-02-03 16:25:51 UTC  
> Url: https://github.com/boostorg/parser/issues/76#issuecomment-1925374455  

Are you sure this one is fixed? THe example above (https://godbolt.org/z/bMv3er6eY) still compiles, even though I would expect it not to.

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-02-04 19:39:53 UTC  
> Url: https://github.com/boostorg/parser/issues/76#issuecomment-1925892958  

It's definitely ill-formed on HEAD of master.  Maybe the single header file is stale?
