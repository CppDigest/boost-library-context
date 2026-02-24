# #35 - key_end!='\0' should be *key_end!='\0' [Closed]

> Username: kivadiu  
> Created at: 2018-06-18 07:18:06 UTC  
> Updated at: 2018-06-18 09:28:14 UTC  
> Closed at: 2018-06-18 09:23:53 UTC  
> Url: https://github.com/boostorg/nowide/issues/35  

This bug was highlighted in the boost review but is still there on master:  
cenv.hpp:104  
while(*key_end!='=' && key_end!='\0')  
should be  
while(*key_end!='=' && *key_end!='\0')

---

## Comment 1

> Username: kivadiu  
> Created at: 2018-06-18 09:21:03 UTC  
> Url: https://github.com/boostorg/nowide/issues/35#issuecomment-397993530  

Fixed here: https://github.com/artyom-beilis/nowide/pull/39

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2018-06-18 09:23:53 UTC  
> Url: https://github.com/boostorg/nowide/issues/35#issuecomment-397994323  

Already fixed in review branch: #14

---

## Comment 3

> Username: kivadiu  
> Created at: 2018-06-18 09:25:43 UTC  
> Url: https://github.com/boostorg/nowide/issues/35#issuecomment-397994829  

But then, can you move it also to master which is the branch people as supposed to clone?

---

## Comment 4

> Username: artyom-beilis  
> Created at: 2018-06-18 09:28:14 UTC  
> Url: https://github.com/boostorg/nowide/issues/35#issuecomment-397995587  

This library is in process of integration of Boost - it is work in progress. There are many changes in review branch including API breaking.  
  
Current master is not going to be relevant soon.
