# #34 - Demonstrate interoperability with Boost.Rational [Closed]

> Username: akrzemi1  
> Created at: 2017-03-11 13:36:48 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-10 22:02:44 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/34  

The following example, which tests usage of `rational<safe<int>>` works fine. I would recommend adding a test like this in the library, and the examples list.  
  
```c++  
#include <safe_integer.hpp>  
#include <iostream>  
#include <limits>  
#include <boost/rational.hpp>  
  
int main(int argc, const char * argv[])  
{  
        using SafeRatio = boost::rational<boost::numeric::safe<int>>;  
        SafeRatio r {1, 2};  
        SafeRatio q {-2, 4};  
        std::cout << (r * q) << std::endl;  
        auto a = r * q;  
        assert ((a == SafeRatio{-1, 4}));  
        SafeRatio x {1, INT_MAX};  
        SafeRatio y {1, 2};  
        try {  
                x * y;  
                assert (false);  
        }  
        catch (std::exception const& e) {  
                assert (true);  
                std::cout << e.what() << std::endl;  
        }  
        x *= 2;  
        x /= 2;  
  
        assert ((x == SafeRatio{1, INT_MAX}));  
        return 0;  
}  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2017-03-11 16:59:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/34#issuecomment-285880788  

Good call.  I had thought of other cases like atomic<safe<int> > and   
perhaps others.  
  
  
On 3/11/17 5:36 AM, Andrzej Krzemieński wrote:  
>  
> The following example, which tests usage of |rational<safe<int>>|   
> works fine. I would recommend adding a test like this in the library,   
> and the examples list.  
>  
> #include  <safe_integer.hpp>  
> #include  <iostream>  
> #include  <limits>  
> #include  <boost/rational.hpp>  
>  
> int  main(int  argc,const  char  * argv[])  
> {  
>          using  SafeRatio = boost::rational<boost::numeric::safe<int>>;  
>          SafeRatio r {1,2};  
>          SafeRatio q {-2,4};  
>          std::cout << (r * q) << std::endl;  
>          auto  a = r * q;  
>          assert  ((a == SafeRatio{-1,4}));  
>          SafeRatio x {1, INT_MAX};  
>          SafeRatio y {1,2};  
>          try  {  
>                  x * y;  
>                  assert  (false);  
>          }  
>          catch  (std::exceptionconst& e) {  
>                  assert  (true);  
>                  std::cout << e.what() << std::endl;  
>          }  
>          x *=2;  
>          x /=2;  
>  
>          assert  ((x == SafeRatio{1, INT_MAX}));  
>          return  0;  
> }  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/issues/34>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3CQ7x6VWqQrpApFAbKuUVANELxVpks5rkqNwgaJpZM4MaNJd>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 2

> Username: robertramey  
> Created at: 2017-06-22 17:24:12 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/34#issuecomment-310446919  

I had remembered to add test/example for using with rational but had forgotten that you included a working demo right here.  Now when I try this test, it doesn't work on my current version of the library.  I've made many, many corrections to the library in accordance with the review conditions and it seems that something has gotten lost in the shuffle.  I'm still looking into this.

---

## Comment 3

> Username: robertramey  
> Created at: 2018-08-10 22:02:44 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/34#issuecomment-412218219  

fixed - see example 15
