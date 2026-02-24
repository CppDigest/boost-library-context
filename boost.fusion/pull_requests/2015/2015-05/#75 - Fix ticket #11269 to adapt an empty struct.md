# #75 Fix ticket #11269 to adapt an empty struct [Merged]

> Username: daminetreg  
> Created at: 2015-05-06 06:29:30 UTC  
> Updated at: 2015-05-06 09:12:23 UTC  
> Merged at: 2015-05-06 07:34:31 UTC  
> Closed at: 2015-05-06 07:34:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/75  

https://svn.boost.org/trac/boost/ticket/11269  
  
This fixes adapting empty structs with BOOST_FUSION_ADAPT_STRUCT as requested in 11269.   
  
Tested on gcc 4.9.2 :   
- `../../../b2 cxxflags="-DBOOST_PP_VARIADICS=0" -a -j8`  
- `../../../b2 cxxflags="-DBOOST_PP_VARIADICS=1" -a -j8`  
  
I'm not sure it's enough as I told on the trac ticket. Do you think there are more use cases adapting empty structs or classes ?  
  
Because this fixes only the specific BOOST_FUSION_ADAPT_STRUCT.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-05-06 07:34:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/75#issuecomment-99355581  

Yes, this kind of thing is always found in Spirit users' code.

---

## Comment 2

> Username: daminetreg  
> Created_at: 2015-05-06 09:12:22 UTC  
> Url: https://github.com/boostorg/fusion/pull/75#issuecomment-99392652  

I'll change the other adapt macros to ensure this is also possible there.  
  
Do you know any example of spirit making this use ? I'm curious for my  
own usage of spirit, because when I was in need of types without parsed  
content in my parsed attributes, I always used a semantic action of the  
form [_val = phx::construct<SomeType>()].  
  
But from what you say it looks like to be doable more automatically. :)

---
