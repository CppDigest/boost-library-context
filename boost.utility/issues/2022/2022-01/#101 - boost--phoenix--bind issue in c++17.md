# #101 - boost::phoenix::bind issue in c++17. [Open]

> Username: notebookdata  
> Created at: 2022-01-10 06:45:18 UTC  
> Updated at: 2022-10-20 09:38:53 UTC  
> Url: https://github.com/boostorg/utility/issues/101  

Hi All,  
  
I am facing these below issues when I upgrade from boost_1_73 and c++14 to boost_1_77 and c++17.  
What will be the problem?  
Error 1:  
include/boost/utility/result_of.hpp:218:8: error: 'GB* (boost::intrusive_ptr<GB>::*)() const noexcept' is not a class, struct, or union type  
  
Error 2:  
include/boost/phoenix/core/detail/function_eval.hpp:119:21: error: no type named 'type' in 'struct boost::result_of<GB* (boost::intrusive_ptr<GB>::* const(boost::intrusive_ptr<GB>&))() const noexcept>'  
  
Thank you.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-01-10 15:36:18 UTC  
> Url: https://github.com/boostorg/utility/issues/101#issuecomment-1284815664  

@mclow: intrusive_ptr belongs to the smart_ptr (https://github.com/boostorg/smart_ptr) module. Could you transfer the issue to that module (it seems that I can't do it with my permissions)?

---

## Comment 2

> Username: pdimov  
> Created at: 2022-10-20 02:16:50 UTC  
> Url: https://github.com/boostorg/utility/issues/101#issuecomment-1284815667  

I don't think this issue has anything to do with `intrusive_ptr`. It's most likely caused by `result_of` not supporting `noexcept` qualified functions.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-10-20 09:29:35 UTC  
> Url: https://github.com/boostorg/utility/issues/101#issuecomment-1285222494  

Please provide a small reproducer.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-10-20 09:38:53 UTC  
> Url: https://github.com/boostorg/utility/issues/101#issuecomment-1285233925  

Something like this I suppose?  
```  
struct GB {};  
  
auto pm = &boost::intrusive_ptr<GB>::get;  
using Pm = decltype(pm);  
  
using R = typename boost::result_of<Pm(GB*)>::type;  
```  
Works under C++14: https://godbolt.org/z/8oY5Keeao  
Fails under C++17: https://godbolt.org/z/EPzzWW34b
