# #305 - Extracting move-only types from unordered_flat_set [Closed]

> Username: Chaosvex  
> Created at: 2025-03-10 05:38:25 UTC  
> Updated at: 2025-04-15 07:38:17 UTC  
> Closed at: 2025-04-15 07:38:17 UTC  
> Url: https://github.com/boostorg/unordered/issues/305  

Looking at [this](https://github.com/boostorg/unordered/issues/273) issue, I'm assuming it's not possible to have a std::*::extract() equivalent here but is there an idiomatic way to extract move-only types (`unique_ptr` in this case) from the container given that the trick in the linked issue can't be used here?  
  
The following workaround... works... but would likely cause a few scowls if found in a production codebase:  
  
```cpp  
#include <memory>  
#include <boost/unordered/unordered_flat_set.hpp>  
#include <print>  
  
struct Foo {  
    int val;  
    Foo(int val) : val(val) { std::println("ctor"); }  
    ~Foo() { std::println("dtor"); }  
};  
  
int main() {  
    boost::unordered_flat_set<std::unique_ptr<Foo>> set;  
    set.emplace(std::make_unique<Foo>(5));  
  
    auto it = set.begin(); // pretend we used .find(...)  
    auto f = &const_cast<std::unique_ptr<Foo>&>(*it); // oof  
    std::unique_ptr<Foo> new_foo(f->release()); // could move instead but...  
    set.erase(it);  
    std::println("{}", new_foo->val);  
}  
```  
```  
ctor  
5  
dtor  
```  
  
Thanks very much!

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-03-11 08:03:56 UTC  
> Url: https://github.com/boostorg/unordered/issues/305#issuecomment-2713069091  

The workaround you propose is admittedly ugly and technically not supported (because of that `const_cast` as you point out), but, well, it will work.

---

## Comment 2

> Username: Chaosvex  
> Created at: 2025-03-11 08:06:39 UTC  
> Url: https://github.com/boostorg/unordered/issues/305#issuecomment-2713076118  

Is there any likelihood of there being a sanctioned way of doing this in the future or is it likely that the design isn't going to allow for it?  
  
Thanks again.

---

## Comment 3

> Username: joaquintides  
> Created at: 2025-03-11 15:45:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/305#issuecomment-2714833254  

The interface of `boost::unordered_flat_set` strives to remain close to that of `std::unordered_set`, so it's unlikely we'll add a facility for this use case.

---

## Comment 4

> Username: pdimov  
> Created at: 2025-03-11 16:51:51 UTC  
> Url: https://github.com/boostorg/unordered/issues/305#issuecomment-2715057004  

Maybe we should, though. This (erase that returns the erased element) is a legitimate operation which `unordered_set` offers in the form of `extract`.  
  
The argument that we don't offer an operation that `unordered_set` does because we strive to remain close to the interface of `unordered_set` doesn't quite make sense.

---

## Comment 5

> Username: joaquintides  
> Created at: 2025-04-15 07:38:17 UTC  
> Url: https://github.com/boostorg/unordered/issues/305#issuecomment-2804117064  

`pull` added in #309.
