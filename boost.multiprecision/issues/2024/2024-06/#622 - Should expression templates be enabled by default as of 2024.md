# #622 - Should expression templates be enabled by default as of 2024? [Open]

> Username: marcovc  
> Created at: 2024-06-10 16:15:24 UTC  
> Updated at: 2025-06-28 19:18:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/622  

Hi,  
  
I just spent  a couple of days hunting a bug that was caused by using "auto" with this library. I then found out that this is expected:  
  
![image](https://github.com/boostorg/multiprecision/assets/624308/12dd47cb-c60c-4c0d-a431-5a83b65a70ab)  
   
I wonder if it is a correct decision today, where "auto" is now very well established into the language, to ship a library that by default computes complete wrong results when using it with "auto".

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-06-10 17:56:47 UTC  
> Updated at: 2024-06-10 17:57:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/622#issuecomment-2158969816  

> spent a couple of days hunting a bug that was caused by using `auto` with this library.  
  
Yes this is a known _feature_. You raise a really good question.  
- Enable `ExpressionTemplates` by default (i.e., leave the library alone)?  
- Disable `ExpressionTemplates` by default? Do we have issues with a breaking change?  
- Eliminate `ExpressionTemplates` entirely? Wow lots of typing and do we have issues with a breaking change?  
  
The final, most obtrusive, option being one I'd preferably not engage on.  
  
I wonder what John thinks of this. We worked hard to get those big numbers. Your intuition might be right from today's perspective. Maybe one day down the road a compiler switch that actively enables _legacy_ could do the trick to get `auto` working from this point forward...  
  
I wonder what @jzmaddock thinks of such evolutions. I'm kind of a _leave_ _it_ _alone_ and documetn it guy.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-06-11 11:15:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/622#issuecomment-2160485477  

+1 on leave it alone, rationale:  
  
1) It's a pretty well known issue, there is a proposal to fix this: https://isocpp.org/files/papers/p0672r0.pdf which uses expression templates as it's motivating factor.  The issue even effects proxy objects like `std::vector<bool>::operator[]`, so we are hardly alone here.  
2) I know we have people that rely on the expression templates for performance reasons - for some of the science applications which can take _days_ to run, even a few percent speedup is vital.  
3) It is documented, and while I realise it's fairly well down the page, there's a big "caution" in the intro that mentions this: https://www.boost.org/doc/libs/1_85_0/libs/multiprecision/doc/html/boost_multiprecision/intro.html.  Of course if you're anything like me, you may well skip the docs entirely if you can ;)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-06-11 11:23:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/622#issuecomment-2160499031  

I've pinged Joel to see if there's any news on the issue (or an alternative fix in C++17/20/23).

---

## Comment 4

> Username: jfalcou  
> Created at: 2024-06-11 12:50:36 UTC  
> Updated at: 2024-06-11 12:53:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/622#issuecomment-2160683713  

P0672 has been left out to die for various reason.  
I had a comprehensive discussion with various people, including Chandler Carruth, that :  
 - this problem needs a solution.  
 - Changing auto is not the way to do it.  
  
I have been not very active for health reasons but it get better since. So if you're interested, we can try to think about a proper solution. I have a bunch of other ideas that may fly further.  
  
The biggest opposition we may face (even if I think it is a bad argument) is that "reflection will fix that anyway". Which is obviously incorrect.  
  
As for language solution, I have now migrated to have constexpr only expression template so that issue happen less. We can also discuss this.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2024-06-13 11:27:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/622#issuecomment-2165381352  

>I have been not very active for health reasons but it get better since. So if you're interested, we can try to think about a proper solution. I have a bunch of other ideas that may fly further.  
  
I'm happy to discuss ideas, but I confess my interest in committee stuff is tiny these days (and I confess I'm out of date with the new language features post C++17).  
  
>The biggest opposition we may face (even if I think it is a bad argument) is that "reflection will fix that anyway". Which is obviously incorrect.  
  
>As for language solution, I have now migrated to have constexpr only expression template so that issue happen less. We can also discuss this.  
  
I'd be interested in knowing how that works.

---

## Comment 6

> Username: piotr5  
> Created at: 2025-05-16 08:15:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/622#issuecomment-2885990972  

I'm coming from the ultimate++ library which is very similar to boost. there they intensively use the "pick-semantics" meaning that no container ever has a copy-constructor and instead the default is to use the move-constructor. and that move-constructor actually does move things, meaning that whatever was owned by a container is then moved to the new container's ownership just because of applying std::move to it. so the object itself remains in the memory it occupied and only the pointer is swapped with the new container's pointer.  on the downside, this only works if both containers understand that pick-semantics, but in case of this warning's actual example: maybe just add some expression-building functions for R-value input? what that function should do is to just disown the cpp_int storing whatever temporaty int and give it the live-time of the expression-template instead. i.e. allow expressions to store the limbs temporarily but do it in constant time without copying. example-code:  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
#include <iostream>  
  
using R=boost::multiprecision::cpp_int;  
  
template<class T>  
auto TT(const T& a, T c){  
    const T t={a[0],c[1]-a[1]};  
    return a[0]*std::move(t[1]);  
}  
int main(){  
    std::array<R,2> d{7,2},e{5,11},f{17,19};  
    std::cout << TT(d,f)<<"\n";  
}  
```  
  
this should not result in bad_alloc for compilers being strict about temporary objects. instead after std::move above the value stored in the 2nd entry of the array t should be in an undefined state while the expression object returned should contain the temporary produced on the way. hard to express the pick-semantics in the documentation though...

---

## Comment 7

> Username: ckormanyos  
> Created at: 2025-06-28 19:16:26 UTC  
> Updated at: 2025-06-28 19:18:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/622#issuecomment-3015991272  

I have thought a long time about this issue.  
  
No is my (subjective) answer to the original query.  
  
Compilers have become better, yes indeed. But changing all the docs and templates is _not_ adding significant value at the expense of huge development time - which is scant (at least for the moment).  
  
So I would suggest to leave the setup the way it is. And then close this issue accordingly.  
  
I do agree with the long-term suggestion and acknowledge that `auto` is a de-facto go-to in programming modern C++. Perhaps we can return to this at a later time?  
  
Cc: @jzmaddock and @mborland
