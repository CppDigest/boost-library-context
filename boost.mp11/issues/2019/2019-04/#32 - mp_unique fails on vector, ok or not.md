# #32 - mp_unique fails on vector, ok or not? [Closed]

> Username: HDembinski  
> Created at: 2019-04-26 12:57:52 UTC  
> Updated at: 2019-12-04 23:50:59 UTC  
> Closed at: 2019-12-04 23:50:59 UTC  
> Url: https://github.com/boostorg/mp11/issues/32  

This code doesn't compile:  
```  
#include <vector>  
#include <boost/mp11/algorithm.hpp>  
  
int main() {  
    using T = std::vector<int>;  
    using U = boost::mp11::mp_unique<T>;  
}  
```  
see https://godbolt.org/z/qt_aZB  
  
This obviously makes no sense, but this is a special path in a generic templated code of mine.  
  
At first, I was confused, thinking that mp11 can handle all kinds of type lists, but then I understood the error better, the point is that std::vector is not a variable length type list, therefore mp_unique correctly fails.  
  
The error is clearly on my side, I violated a precondition of mp_unique and many other mp11 meta-functions. However, some meta-functions work fine even with a std::vector, namely those which do not mutate the type list. mp_rename, mp_size, etc all work on std::vector.  
  
So I was wondering, should the algorithms be classified in those with work with immutable type lists and those with do not? Should this be documented for each meta-function?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-26 15:21:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/32#issuecomment-487096546  

The general rule is that functions that don't need to change the number of elements work, and those that do, don't work.  
  
That said, `std::vector<int>` is in reality `std::vector<int, std::allocator<int>>`, which further complicates how the algorithms see it. It has an `mp_size` of 2 for example.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-04-26 17:24:47 UTC  
> Url: https://github.com/boostorg/mp11/issues/32#issuecomment-487135620  

True.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-04-26 17:26:38 UTC  
> Url: https://github.com/boostorg/mp11/issues/32#issuecomment-487136262  

Feel free to close this issue, I just wanted to pose the question, I don't have any preferred solution.
