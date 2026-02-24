# #17 Support of reference polymorphic_downcast [Merged]

> Username: JulienDelacroix  
> Created at: 2019-12-05 15:25:07 UTC  
> Updated at: 2020-01-25 08:00:27 UTC  
> Merged at: 2020-01-25 07:59:41 UTC  
> Closed at: 2020-01-25 07:59:41 UTC  
> Url: https://github.com/boostorg/conversion/pull/17  

boost::polymorphic_downcast only supports raw pointer cast, which is a gap compared to static_cast.  
  
To make it clear:  
       Base* base = new Base();  
  
       static_cast<Derived*>(base)                 ===> OK  
       static_cast<Derived&>(*base)                ===> OK  
  
       boost::polymorphic_downcast<Derived*>(base)  ===> OK  
       boost::polymorphic_downcast<Derived&>(*base) ===> IMPOSSIBLE  
  
boost::polymorphic_downcast being a kind of "debug-safe version" of static_cast, it should provide the same capabilities.  
This patch extend boost::polymorphic_downcast to support references just like static_cast does.  
This is achieved by introducing a partial specialization for references.  
Unit tests have been updated accordingly.

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2019-12-07 13:24:17 UTC  
> Url: https://github.com/boostorg/conversion/pull/17#issuecomment-562851049  

Hi @JulienDelacroix,  
  
You have wrongly committed a "bin" file, please remove it ;)  
  
Cheers,  
Romain

---

## Comment 2

> Username: apolukhin  
> Created_at: 2020-01-25 08:00:27 UTC  
> Url: https://github.com/boostorg/conversion/pull/17#issuecomment-578386338  

Many thanks for the PR!

---
