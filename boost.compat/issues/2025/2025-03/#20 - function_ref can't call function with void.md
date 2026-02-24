# #20 - function_ref can't call function with void* [Closed]

> Username: GitSparTV  
> Created at: 2025-03-26 20:31:47 UTC  
> Updated at: 2025-03-26 22:25:55 UTC  
> Closed at: 2025-03-26 22:25:01 UTC  
> Url: https://github.com/boostorg/compat/issues/20  

Hello, I use `function_ref` on function with signature: `void(void*)`, however, if I pass `void*` as I expect, it doesn't compile.  
It starts to work if I get address of the `void*` or move it, which is weird and I don't want this.  
  
```cpp  
#include <boost/compat/function_ref.hpp>  
#include <iostream>  
  
int main() {  
    auto lambda = [](void* p) { std::cout << p << std::endl; };  
  
    boost::compat::function_ref<void(void*)> f = lambda;  
  
    void* p = new int;  
  
    std::cout << p << std::endl;  
    std::cout << &p << std::endl;  
  
    lambda(p);        // OK  
    f(&p);            // OK  
    f(std::move(p));  // OK  
    f(p);             // ERROR  
}  
```  
  
```txt  
error: no matching function for call to object of type 'boost::compat::function_ref<void (void *)>'  
```  
  
https://godbolt.org/z/f8P53PjnG

---

## Comment 1

> Username: mclow  
> Created at: 2025-03-26 20:51:34 UTC  
> Url: https://github.com/boostorg/compat/issues/20#issuecomment-2755719897  

I just tried this with a reasonably current boost trunk and Apple clang (Apple clang version 16.0.0 (clang-1600.0.26.6)) and your code compiled w/o an error.

---

## Comment 2

> Username: mclow  
> Created at: 2025-03-26 20:52:24 UTC  
> Url: https://github.com/boostorg/compat/issues/20#issuecomment-2755721497  

Can you try your code with the 1.88.0 beta release?  https://archives.boost.io/beta/1.88.0.beta1/source/

---

## Comment 3

> Username: GitSparTV  
> Created at: 2025-03-26 20:54:07 UTC  
> Updated at: 2025-03-26 20:54:43 UTC  
> Url: https://github.com/boostorg/compat/issues/20#issuecomment-2755724809  

No, unfortunately, I don't have it. But if you say so, I will take a note for the future.  
We use stable releases

---

## Comment 4

> Username: cmazakas  
> Created at: 2025-03-26 22:06:24 UTC  
> Url: https://github.com/boostorg/compat/issues/20#issuecomment-2755849222  

Ha ha, yeah, this was a bit of a silly snafu on our end.  
  
We've address this here: https://github.com/boostorg/compat/pull/17  
  
This commit exists on master: https://github.com/boostorg/compat/commits/master/  
  
So, you should see this be automatically fixed in the upcoming Boost release.

---

## Comment 5

> Username: GitSparTV  
> Created at: 2025-03-26 22:25:55 UTC  
> Url: https://github.com/boostorg/compat/issues/20#issuecomment-2755877736  

Thanks.  
Duplicate of #17
