# #61 Assert when we try to use an incomplete type on a trait which require… [Merged]

> Username: jzmaddock  
> Created at: 2018-02-04 10:04:04 UTC  
> Updated at: 2018-02-04 19:27:30 UTC  
> Merged at: 2018-02-04 19:27:24 UTC  
> Closed at: 2018-02-04 19:27:24 UTC  
> Url: https://github.com/boostorg/type_traits/pull/61  

…s complete types as arguments.  
  
This prevents some traits from doing the wrong thing (ie compiling but giving the wrong answer when handed an incomplete type.  
See https://svn.boost.org/trac10/ticket/12285.  
A by-product of this is we add is_complete as a new trait.

---
