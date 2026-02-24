# #229 - Replace Boost.MPL with Boost.MP11 [Closed]

> Username: mloskot  
> Created at: 2019-02-02 00:37:01 UTC  
> Updated at: 2019-04-16 09:16:54 UTC  
> Closed at: 2019-04-14 20:13:46 UTC  
> Url: https://github.com/boostorg/gil/issues/229  

The MP11, https://www.boost.org/libs/mp11/, is a modern alternative.  
  
The major benefits of switching to MP11 are:  
* Perfect integration with C++11 `<type_traits>` and later  
* It should allow faster compilation  
* Often may lead to simpler constructs  
  
### References  
  
* #228 - ~considered a blocker~

---

## Comment 1

> Username: Mike-Devel  
> Created at: 2019-04-16 08:44:26 UTC  
> Url: https://github.com/boostorg/gil/issues/229#issuecomment-483568758  

Just wondering: Gil is still including some mpl headers in two files. Have they been overlooked or is the transition just not complete yet?   
  
- boost/gil/extension/dynamic_image/reduce.hpp:  
  
https://github.com/boostorg/gil/blob/6a5772144b5dd030045acc1bb7d92d73e1bb43c3/include/boost/gil/extension/dynamic_image/reduce.hpp#L22-L30  
  
- boost/gil/promote_integral.hpp: https://github.com/boostorg/gil/blob/6a5772144b5dd030045acc1bb7d92d73e1bb43c3/include/boost/gil/promote_integral.hpp#L19-L23

---

## Comment 2

> Username: mloskot  
> Created at: 2019-04-16 08:48:23 UTC  
> Updated at: 2019-04-16 08:48:35 UTC  
> Url: https://github.com/boostorg/gil/issues/229#issuecomment-483570152  

@Mike-Devel Thanks for an extra pair of eyes.  
No they have not been overlooked but those two have been postponed, per the *Tasklist* of #274, as separate issues:  
* #280  
* #281   
  
My gut feeling makes me inclined to remove the `reduce.hpp`, but this need to be checked more carefully.

---

## Comment 3

> Username: Mike-Devel  
> Created at: 2019-04-16 08:56:06 UTC  
> Url: https://github.com/boostorg/gil/issues/229#issuecomment-483572801  

Was just about to delete the question when I found the issues. Sorry for the noise and thanks for the quick response.

---

## Comment 4

> Username: mloskot  
> Created at: 2019-04-16 09:16:54 UTC  
> Url: https://github.com/boostorg/gil/issues/229#issuecomment-483579803  

No worries. As I said, I appreciate anyone who is double-checking my steps.
