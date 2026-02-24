# #11 Continuation of #7 - removal of safe_bool idiom [Closed]

> Username: jeking3  
> Created at: 2018-10-13 13:34:48 UTC  
> Updated at: 2018-10-13 15:38:12 UTC  
> Closed at: 2018-10-13 15:38:12 UTC  
> Url: https://github.com/boostorg/logic/pull/11  

See #7 for original description and motivation.  The removal of safe_bool had no impact on the behavior of the class, so I simplified the original PR and added a test to explain it better.  Hopefully I got it right. :)

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-10-13 15:07:45 UTC  
> Url: https://github.com/boostorg/logic/pull/11#issuecomment-429549360  

AMDG  
  
From your previous comment and the contents of the test  
case, I get the impression that you don't understand  
what safe_bool is supposed to prevent.  
  
James E. King III wrote:  
> Applying Robert's patch does nothing to change this behavior, therefore I agree with Robert that safe_bool is unnecessary and can be removed.  
Robert Ramey wrote:  
> if(f < t) ...  
> if(t >> 1) ...  
>   
> fail to compile currently but will compile with this change  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-10-13 15:13:14 UTC  
> Updated_at: 2018-10-13 15:13:53 UTC  
> Url: https://github.com/boostorg/logic/pull/11#issuecomment-429549728  

Thanks, I'll take a look at it again.  (see the end of the original description in https://svn.boost.org/trac10/ticket/5510)

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-10-13 15:36:10 UTC  
> Url: https://github.com/boostorg/logic/pull/11#issuecomment-429551328  

So safe_bool is there to deal with the fact that C++03 handles bool as a numeric type, so if there is an implicit conversion operator to bool() then that is just "if (0 < 1)", however without an implicit conversion operator to bool() no numeric comparison is possible.  So the code I picked up and tried to optimize actually broke it, and the original PR was also unnecessary following the explicit keyword fix from @pdimov.  Then perhaps the right thing to do here is instead just the following, leaving the bits safe_bool needs for C++03, but omitting it from C++11 and later which achieves some of the intention of Robert's original PR.  
```  
class tribool  
{  
#if defined( BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS )  
private:  
  /// INTERNAL ONLY  
  struct dummy {  
    void nonnull() {};  
  };  
  
  typedef void (dummy::*safe_bool)();  
#endif  
```

---
