# #32 - Detect unsupported compilers [Closed]

> Username: akrzemi1  
> Created at: 2017-03-10 07:38:27 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-10 22:03:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/32  

I was testing safe_numerics on different compilers that claim to support C++14 (at least they have the -std=c++14 switch), but apparently they are missing some tiny C++14 features. The effect is that examples compile fine up to a certain point, and then I get a message like "missing parentheses", which leads me into thinking that you have a bug in the implementation somewhere.   
  
Of course, this is not the case, if I try it on the newest compiler, my examples works fine. But the first impression is "this library has obvious bugs, it does not even compile". What would help avoid such situations, is if the library detected what feature support is missing from the compiler and report it up front directly into the face of developer.

---

## Comment 1

> Username: robertramey  
> Created at: 2017-04-09 18:28:13 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/32#issuecomment-292803194  

This is interesting but outside of what I can do.  
  
If you come upon such a case and it's included as a feature macro in   
boost/config.hpp I can stop the compile with a good message.  If there   
isn't such a macro, the best would be to make  a suggestion to the   
maintainer of Boost/config to add one then I can tweak the code to use   
it.   These will have to be dealt with on a case by case basis as part   
of the maintenance process.  
  
  
On 3/9/17 11:38 PM, Andrzej Krzemieński wrote:  
>  
> Of course, this is not the case, if I try it on the newest compiler,   
> my examples works fine. But the first impression is "this library has   
> obvious bugs, it does not even compile". What would help avoid such   
> situations, is if the library detected what feature support is missing   
> from the compiler and report it up front directly into the face of   
> developer.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793
