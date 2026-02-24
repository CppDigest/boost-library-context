# #106 - Escaped HTML chars in reference [Closed]

> Username: alandefreitas  
> Created at: 2021-10-20 22:10:00 UTC  
> Updated at: 2022-01-27 03:01:37 UTC  
> Closed at: 2022-01-24 19:16:24 UTC  
> Url: https://github.com/boostorg/docca/issues/106  

# Description  
  
Some names in the reference include HTML escaped chars instead of the chars in the source code.  
   
# Preview  
  
This [example](https://alandefreitas.github.io/boost-utility-docs/utility/ref/boost__operators_lt_T.html) (`,T__gt boost::operators<T, T >`) generated from [this class](https://github.com/alandefreitas/utility/blob/207ecf067dc07af9b23bb63d5354a666681202b7/include/boost/operators_v1.hpp#L880) (`template<class T> struct operators<T, T >`).  
  
# Replicating the issue  
  
```bash  
git clone -b docca_ref https://github.com/alandefreitas/utility.git  
cd utility/doc  
b2  
```  
  
File `./html/utility/ref/boost__operators_lt_T.html`  
  
(I'm still coming up with a smaller example to replicate the issue.)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-20 23:29:53 UTC  
> Url: https://github.com/boostorg/docca/issues/106#issuecomment-948111333  

This probably has to do with the fact that the struct's name has the prefix `operator`

---

## Comment 2

> Username: evanlenz  
> Created at: 2022-01-24 07:27:50 UTC  
> Updated at: 2022-01-24 07:28:32 UTC  
> Url: https://github.com/boostorg/docca/issues/106#issuecomment-1019795986  

@alandefreitas Would it be convenient for you to see if this is still an issue (that is, the undesired/buggy behavior is still present in docca)?

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-01-24 19:04:07 UTC  
> Url: https://github.com/boostorg/docca/issues/106#issuecomment-1020441508  

The code on which this was based doesn't exist anymore, but I replicated the code with these classes and the output from docca is ok.

---

## Comment 4

> Username: evanlenz  
> Created at: 2022-01-24 19:16:24 UTC  
> Url: https://github.com/boostorg/docca/issues/106#issuecomment-1020453293  

Oh cool, thanks for checking! I had a feeling it might have been fixed with some more recent changes making replacements more robust. I will close now. (Please correct me if I misunderstood, but it sounds like the issue is obsolete now.)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-01-25 15:03:42 UTC  
> Url: https://github.com/boostorg/docca/issues/106#issuecomment-1021278414  

do we have a test?

---

## Comment 6

> Username: evanlenz  
> Created at: 2022-01-26 22:10:38 UTC  
> Url: https://github.com/boostorg/docca/issues/106#issuecomment-1022649782  

I had tried creating one locally (though I don't know if I did it correctly), couldn't reproduce the problem, and then checked back here. I could check it in for review, if you like.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-01-27 03:01:37 UTC  
> Url: https://github.com/boostorg/docca/issues/106#issuecomment-1022800723  

Sure if you submit it, @alandefreitas can review it
