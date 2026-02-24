# #68 - Change in allowed code with boost 1.83 [Closed]

> Username: foutrelis  
> Created at: 2023-09-01 16:57:02 UTC  
> Updated at: 2024-03-04 17:12:31 UTC  
> Closed at: 2023-09-03 19:16:51 UTC  
> Url: https://github.com/boostorg/signals2/issues/68  

The following example code used to compile with boost 1.81 but not with 1.83. Likely a behavior change after https://github.com/boostorg/signals2/commit/7a16fc1405f01e13463b6ce7723f30f40ba41fa4.  
  
```  
#include <iostream>  
#include <boost/signals2/signal.hpp>  
  
void foo() { std::cout << "foo" << std::endl; }  
void bar() { std::cout << "bar" << std::endl; }  
  
int main() {  
  boost::signals2::signal<void ()> sig;  
  sig.connect(foo);  
  sig.connect(bar);  
  sig.disconnect(bar);  
  sig();  
}  
```  
  
The above faiils to compile with:  
  
```  
/usr/include/boost/function/function_base.hpp:651:14: error: invalid ‘static_cast’ from type  
‘boost::detail::function::function_buffer_members::obj_ptr_t’ {aka ‘void*’} to type ‘void (*)()’  
  651 |       return static_cast<const Functor*>(type_result.members.obj_ptr);  
```  
  
Changing the disconnect call to `sig.disconnect(&bar);` allows it to compile. I'm just not sure if this is expected or not, so I thought I'd ask anyway.

---

## Comment 1

> Username: fmhess  
> Created at: 2023-09-01 19:28:34 UTC  
> Url: https://github.com/boostorg/signals2/issues/68#issuecomment-1703230876  

Verified, I guess removing the passing of the function to the == operator prevents the function argument from implicitly converting to a function pointer.

---

## Comment 2

> Username: fmhess  
> Created at: 2023-09-01 20:24:59 UTC  
> Url: https://github.com/boostorg/signals2/issues/68#issuecomment-1703283297  

I'm kind of inclined to ignore this, but I'll try punting to Boost.Function and see how they feel about it.  
  
https://github.com/boostorg/function/issues/46

---

## Comment 3

> Username: foutrelis  
> Created at: 2023-09-03 19:16:51 UTC  
> Url: https://github.com/boostorg/signals2/issues/68#issuecomment-1704380256  

Fixed in Boost.Function. @fmhess thanks for bringing it to their attention. :)

---

## Comment 4

> Username: Mohsensafcar  
> Created at: 2024-03-04 17:12:29 UTC  
> Url: https://github.com/boostorg/signals2/issues/68#issuecomment-1977076180  

Fixok43z7o
