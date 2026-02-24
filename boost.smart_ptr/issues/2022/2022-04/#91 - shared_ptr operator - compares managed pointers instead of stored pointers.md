# #91 - shared_ptr operator < compares managed pointers instead of stored pointers [Open]

> Username: JoergRuedenauer  
> Created at: 2022-04-13 09:18:32 UTC  
> Updated at: 2022-05-19 19:27:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/91  

operator < of boost::shared_ptr compares the managed pointers:  
```  
template<class T, class U> inline bool operator<(shared_ptr<T> const & a, shared_ptr<U> const & b) BOOST_SP_NOEXCEPT  
{  
    return a.owner_before( b );  
}  
template<class Y> bool owner_before( shared_ptr<Y> const & rhs ) const BOOST_SP_NOEXCEPT  
{  
    return pn < rhs.pn;  
}  
```  
This is also documented as   
> under the equivalence relation defined by operator<, !(a < b) && !(b < a), two shared_ptr instances are equivalent if and only if they share ownership or are both empty.  
  
However, std::shared_ptr compares the stored pointers. Comparing the managed pointers defeats the purpose of the aliasing constructor and is asymmetrical to operator==, which _does_ compare the stored pointers. See https://en.cppreference.com/w/cpp/memory/shared_ptr/operator_cmp:   
> In all cases, it is the stored pointer (the one returned by [get()](https://en.cppreference.com/w/cpp/memory/shared_ptr/get)) that is compared, rather than the managed pointer (the one passed to the deleter when [use_count](https://en.cppreference.com/w/cpp/memory/shared_ptr/use_count) goes to zero). The two pointers may differ in a [shared_ptr](https://en.cppreference.com/w/cpp/memory/shared_ptr) created using the aliasing constructor.  
  
See a minimal repro here: https://godbolt.org/z/xon8cxnss

---

## Comment 1

> Username: pdimov  
> Created at: 2022-05-19 19:27:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/91#issuecomment-1132117023  

The current semantics of `operator<` are intentional. They predate `std::shared_ptr`. The committee changed `operator<` for `shared_ptr` to compare the pointer values, but that was after `shared_ptr` was added to the standard.  
  
(`std::shared_ptr` also defines the rest of the comparison operators, and we do not.)
