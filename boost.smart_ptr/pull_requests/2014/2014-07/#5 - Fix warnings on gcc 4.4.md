# #5 Fix warnings on gcc 4.4 [Merged]

> Username: Lastique  
> Created at: 2014-07-14 18:33:39 UTC  
> Updated at: 2014-07-15 09:53:15 UTC  
> Merged at: 2014-07-15 09:30:54 UTC  
> Closed at: 2014-07-15 09:30:54 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/5  

Added a special version of sp_forward for an outdated version of rvalue references supported by gcc 4.4. The compiler would create a temporary and return an rvalue reference to it in the original code. This resulted in warnings about 'returning reference to temporary'.  
  
The added version is similar to std::forward on that compiler, except it doesn't prohibit template argument deduction (which is in line with the original sp_forward).

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-07-14 18:34:32 UTC  
> Updated_at: 2014-07-14 18:37:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/5#issuecomment-48938615  

In case you need a test case to reproduce the warning:  
  
```  
  #include <boost/smart_ptr/shared_ptr.hpp>  
  #include <boost/smart_ptr/make_shared_object.hpp>  
  
  struct my_struct  
  {  
    int n;  
  
    explicit my_struct(int&& x) : n(x) {}  
  };  
  
  int main()  
  {  
    boost::shared_ptr< my_struct > p = boost::make_shared< my_struct >(10);  
    return p->n;  
  }  
```

---

## Comment 2

> Username: pdimov  
> Created_at: 2014-07-15 09:33:59 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/5#issuecomment-49009784  

Do you need this to be merged to master for 1.56, or can it wait?

---

## Comment 3

> Username: Lastique  
> Created_at: 2014-07-15 09:53:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/5#issuecomment-49011462  

I'd prefer it to be in 1.56, if possible. The change seems fairly simple.

---
