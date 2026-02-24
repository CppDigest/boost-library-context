# #78 - The 8bbcca5341fa3eabb6d0ab83c5f655ccceabf41f breaks library for mingw(-64)/gcc [Closed]

> Username: eldiener  
> Created at: 2017-11-15 01:28:19 UTC  
> Updated at: 2017-11-27 17:39:33 UTC  
> Closed at: 2017-11-27 17:26:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/78  

SHA 8bbcca5341fa3eabb6d0ab83c5f655ccceabf41f breaks the build of the serialization library for mingw(-64)/gcc. None of the updates after that fix the library for mingw(-64)/gcc. In the update prior to 8bbcca5341fa3eabb6d0ab83c5f655ccceabf41f the library builds without error for mingw(-64)/gcc.

---

## Comment 1

> Username: eldiener  
> Created at: 2017-11-17 08:33:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/78#issuecomment-345178698  

A quick note that the update above that breaks the build of the serialization library for mingw(-64)/gcc says in the git log:  
  
"* fixed addressor fiasco  
simplified singleton visibility  
Jamfile  
added boost/system to requirements  
corrected inclusion tests to include link to serialization library"

---

## Comment 2

> Username: eldiener  
> Created at: 2017-11-27 17:26:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/78#issuecomment-347257539  

I am glad to see this has now been fixed. Thanks !

---

## Comment 3

> Username: robertramey  
> Created at: 2017-11-27 17:39:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/78#issuecomment-347262145  

Appreciate the fact that you're pointing this stuff out.  I strive for perfection - but it's not easy in this world!  Thanks for you help.
