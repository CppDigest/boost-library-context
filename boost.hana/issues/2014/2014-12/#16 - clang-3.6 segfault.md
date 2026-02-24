# #16 - clang-3.6 segfault [Closed]

> Username: Iced-Sun  
> Created at: 2014-12-23 13:59:47 UTC  
> Updated at: 2015-01-12 18:03:31 UTC  
> Closed at: 2015-01-12 18:03:31 UTC  
> Url: https://github.com/boostorg/hana/issues/16  

The following snippet causes clang-3.6 segfault.  
  
``` c++  
#include <boost/hana.hpp>  
  
auto make_string = [](auto key) {  
    return BOOST_HANA_STRING(key);  
};  
  
int main() {  
    make_string("yes");  
}  
```  
  
Am I misusing anything?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-01-12 18:03:31 UTC  
> Url: https://github.com/boostorg/hana/issues/16#issuecomment-69614714  

First of all, I apologize for the super late answer. I was on a trip and internet access was scarce. You are misusing the `BOOST_HANA_STRING` macro; you can only pass it a string literal. That being said, the segfault is a Clang bug and I would expect a compilation error related to the macro's implementation instead.
