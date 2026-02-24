# #589 Remove boost.lexical cast and boost.core dependencies [Closed]

> Username: mborland  
> Created at: 2021-03-29 17:06:20 UTC  
> Updated at: 2021-04-02 16:49:57 UTC  
> Closed at: 2021-04-02 11:53:22 UTC  
> Url: https://github.com/boostorg/math/pull/589  



---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2021-03-30 19:14:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/589#pullrequestreview-624617883  

📁 include/boost/math/interpolators/detail/barycentric_rational_detail.hpp

```diff
 141 |                       #else
 142 |+                       + std::string("smaller than the epsilon of ")
 143 |                       + std::string(typeid(Real).name());
```

> Username: NAThompson  
> Created_at: 2021-03-30 19:14:14 UTC  
> Updated_at: 2021-03-31 16:49:55 UTC  
> Url: https://github.com/boostorg/math/pull/589#discussion_r604365761  

I'd remove the ifelse here and just do the `std::string(typeid(Real).name())`. I mean, the demangler is cool but I'm not sure it's worth a dependency and workarounds.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-04-02 08:25:55 UTC  
> Url: https://github.com/boostorg/math/pull/589#issuecomment-812411781  

All the CI failures on this one are: "GitHub Actions has encountered an internal error when running your job." Should be good to go.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-04-02 10:11:20 UTC  
> Url: https://github.com/boostorg/math/pull/589#issuecomment-812466946  

@mborland : try compiling :  
  
```  
g++ -I. -c -DBOOST_MATH_STANDALONE libs/math/test/compile_test/*.cpp  
```  
  
and there are gazillions of errors.  The first is caused by a `static_assert(false` which is *always* triggered by gcc, not just when the containing function is instantiated, the condition has to depend on a dependent template type parameter, the next by use of BOOST_STRINGIZE.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-04-02 10:35:07 UTC  
> Url: https://github.com/boostorg/math/pull/589#issuecomment-812474990  

> @mborland : try compiling :  
>   
> ```  
> g++ -I. -c -DBOOST_MATH_STANDALONE libs/math/test/compile_test/*.cpp  
> ```  
>   
> and there are gazillions of errors. The first is caused by a `static_assert(false` which is _always_ triggered by gcc, not just when the containing function is instantiated, the condition has to depend on a dependent template type parameter, the next by use of BOOST_STRINGIZE.  
  
You are correct. I have a branch that contains this PR and #588 so that I could begin fully testing Standalone. It has several little fixes like the ones you described. Are you good with me closing both of those PRs and rolling them into one?

---

## Comment 5

> Username: mborland  
> Created_at: 2021-04-02 11:53:21 UTC  
> Url: https://github.com/boostorg/math/pull/589#issuecomment-812498111  

Consolidated into #597

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-04-02 16:49:57 UTC  
> Url: https://github.com/boostorg/math/pull/589#issuecomment-812612271  

> Are you good with me closing both of those PRs and rolling them into one?  
  
Nod.  Sounds good to me!

---
