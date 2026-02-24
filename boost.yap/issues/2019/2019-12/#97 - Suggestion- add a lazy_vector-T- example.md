# #97 - Suggestion: add a lazy_vector<T> example [Open]

> Username: greole  
> Created at: 2019-12-04 11:03:15 UTC  
> Updated at: 2020-01-13 10:05:14 UTC  
> Url: https://github.com/boostorg/yap/issues/97  

First, thanks for creating yap! I am trying to extend the lazy_vector example to create a `lazy_vector<T>` version. This would need another template parameter for the `lazy_vector_expr`  
struct, like   
  
```  
template <boost::yap::expr_kind Kind, typename Tuple, typename I>  
struct lazy_vector_expr  
{  
    static const boost::yap::expr_kind kind = Kind;  
  
    Tuple elements;  
  
    // Note that this does not return an expression; it is greedily evaluated.  
    I operator[] (std::size_t n) const;  
};  
```  
However, this doesn't work with the provided macros `BOOST_YAP_USER_BINARY_OPERATOR`. Does it mean i should implement a different version of   `BOOST_YAP_USER_BINARY_OPERATOR`? What is the best way to achieve this?

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-01-01 20:12:59 UTC  
> Url: https://github.com/boostorg/yap/issues/97#issuecomment-570080299  

Interesting!  I think maybe making macros that take a variadic number of arguments at the end might work -- that way, we can provide the extra "typename I" in your example, or "std::size_t N" in some other case.  Those would get passe through.  PR welcome; otherwise, I may take a while to get to this -- I'm readying another library for including into Boost.

---

## Comment 2

> Username: greole  
> Created at: 2020-01-13 10:05:14 UTC  
> Url: https://github.com/boostorg/yap/issues/97#issuecomment-573587926  

@tzlaine Ok I'll see if I can't implement it myself and do a PR.
