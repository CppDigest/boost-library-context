# #4 Avoided GCC 4.9 regression in C++14 mode [Closed]

> Username: Flast  
> Created at: 2014-10-12 07:34:47 UTC  
> Updated at: 2014-10-12 09:25:25 UTC  
> Closed at: 2014-10-12 09:10:50 UTC  
> Url: https://github.com/boostorg/move/pull/4  

GCC 4.9.0 and 4.9.1 have a regression: don't compile parenthesized return stmt in C++14 mode.  
  
```  
movable f()  
{  
    movable m;  
    return (m); // bug on here  
}  
```  
  
This PR will fix [BP x86_64 C++11 - move - doc_move_return / gcc-4.9.1~c14](http://www.boost.org/development/tests/develop/developer/output/BP%20x86_64%20C++11-boost-bin-v2-libs-move-example-doc_move_return-test-gcc-4-9-1~c14-debug-debug-symbols-off-link-static.html), and other test failures.  
  
For more details, see: https://gcc.gnu.org/PR63437 .

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-10-12 07:52:44 UTC  
> Url: https://github.com/boostorg/move/pull/4#issuecomment-58776618  

Wouldn't this disallow Named Return Optimization for C++14 in GCC 4.9? I have no access to this compiler but maybe replacing:  
      #define BOOST_MOVE_RET(RET_TYPE, REF)\  
         (REF)  
      //  
  
with   
  
```  
  #define BOOST_MOVE_RET(RET_TYPE, REF)\  
     REF  
  //  
```  
  
could be a better fix. Could you try it, please?

---

## Comment 2

> Username: Flast  
> Created_at: 2014-10-12 08:30:57 UTC  
> Url: https://github.com/boostorg/move/pull/4#issuecomment-58777518  

> ```  
>   #define BOOST_MOVE_RET(RET_TYPE, REF)\  
>      REF  
>   //  
> ```  
  
Ah, yes. It should work. I'll resend a patch.

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2014-10-12 08:34:35 UTC  
> Url: https://github.com/boostorg/move/pull/4#issuecomment-58777583  

I've committed:  
  
SHA-1: 1f6892c935633a322a8f0464ef34810c9a613d6e  
- Fix GCC-4.9 c++14 regression (see https://gcc.gnu.org/PR63437) avoiding parenthesied BOOST_MOVE_RETURN.  
  
Let's see if this fixes the problem. Thanks for the report.

---

## Comment 4

> Username: Flast  
> Created_at: 2014-10-12 09:10:50 UTC  
> Url: https://github.com/boostorg/move/pull/4#issuecomment-58779774  

Good! It works fine me!

---
