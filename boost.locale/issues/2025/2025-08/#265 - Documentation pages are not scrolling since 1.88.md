# #265 - Documentation pages are not scrolling since 1.88 [Closed]

> Username: artyom-beilis  
> Created at: 2025-08-26 08:02:33 UTC  
> Updated at: 2025-09-17 11:13:05 UTC  
> Closed at: 2025-09-17 11:13:05 UTC  
> Url: https://github.com/boostorg/locale/issues/265  

If you open documentation pages starting from version 1.88 it is impossible to scroll it down:  
  
https://www.boost.org/doc/libs/1_88_0/libs/locale/doc/html/using_localization_backends.html  
  
Before that it worked fine you could scroll to the end  
  
https://www.boost.org/doc/libs/1_87_0/libs/locale/doc/html/using_localization_backends.html  
  
It is relevant for all pages not just this specific one  
  
I checked in chrome, firefox and edge.  
  
Thanks!

---

## Comment 1

> Username: omor1  
> Created at: 2025-09-03 18:43:19 UTC  
> Url: https://github.com/boostorg/locale/issues/265#issuecomment-3250357825  

I just encountered this as well. Not sure if this is an issue with [boostorg/locale](https://github.com/boostorg/locale) or if it should be filed with [boostorg/website-v2-docs](https://github.com/boostorg/website-v2-docs) or [boostorg/website-v2](https://github.com/boostorg/website-v2).

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-09-04 06:50:40 UTC  
> Url: https://github.com/boostorg/locale/issues/265#issuecomment-3252178726  

It's actually a bug in the doc-generation tool (doxygen) used for those during the official release process introduced by an update to that.  
The docs for the develop and master branch already work again, e.g. https://www.boost.org/doc/libs/master/libs/locale/doc/html/using_localization_backends.html

---

## Comment 3

> Username: sdarwin  
> Created at: 2025-09-04 15:36:14 UTC  
> Url: https://github.com/boostorg/locale/issues/265#issuecomment-3254273863  

To anyone who encounters this bug, please use the master branch docs until the fix is propagated.    
  
The docs for the develop and master branch already work again, e.g. https://www.boost.org/doc/libs/master/libs/locale/doc/html/using_localization_backends.html

---

## Comment 4

> Username: Flamefire  
> Created at: 2025-09-17 11:13:05 UTC  
> Url: https://github.com/boostorg/locale/issues/265#issuecomment-3302504051  

Documentation for 1.88 & 1.89 were regenerated and just updated. They are now scrolling again  
  
Thanks for the report!
