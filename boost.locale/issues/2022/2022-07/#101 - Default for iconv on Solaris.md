# #101 - Default for iconv on Solaris? [Closed]

> Username: Flamefire  
> Created at: 2022-07-06 15:43:47 UTC  
> Updated at: 2022-07-07 13:00:16 UTC  
> Closed at: 2022-07-07 13:00:16 UTC  
> Url: https://github.com/boostorg/locale/issues/101  

@artyom-beilis What is the intended semantic behind https://github.com/boostorg/locale/blob/f44b3bb771f01fb94614260d57e1d09cdfe1cdff/build/Jamfile.v2#L212-L214   
  
Should that mean either:  
- Never use Iconv on Solaris ?   
- Default to not using Iconv on Solaris ?

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2022-07-07 06:36:02 UTC  
> Updated at: 2022-07-07 06:36:48 UTC  
> Url: https://github.com/boostorg/locale/issues/101#issuecomment-1177151253  

There are two variants of iconv on Solaris:  
  
1. One the comes with OS it is buggy  
2. There is GNU iconv it is fine  
  
So I try to avoid using iconv on Solaris in general unless user explicitly asks for.  
  
So the logic is following I use iconv under two conditions  
  
1. User explicitly asked for iconv (maybe because he wants to use GNU iconv)  
2. We are not on Solaris - we use it unless the user set iconv explicitly to off.  
  
So ICU uconv API is preferred on Solaris.

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-07-07 07:24:03 UTC  
> Url: https://github.com/boostorg/locale/issues/101#issuecomment-1177188391  

Ok then the condition may be wrong and *always* disable Iconv on Solaris due to undefined operator precedence in B2.  
I included parens and a comment to explain this at https://github.com/boostorg/locale/pull/100/commits/58c06603bd6a78fe1a577478fdfee9764c4126df#diff-322788b77c72dedcc9b4a12bf0233ceb8451b4a6baa6d851173ecd5320ac14aeR205-R208

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2022-07-07 07:52:01 UTC  
> Url: https://github.com/boostorg/locale/issues/101#issuecomment-1177213171  

Ohhhh, I see, didn't know that precedence is undefined.   
  
I hope Boost will switch to CMake finally after 10 years of discussion (wishful thinking)
