# #73 - boost documentation bug [Open]

> Username: dtkerns  
> Created at: 2018-09-19 21:07:44 UTC  
> Updated at: 2018-12-18 23:01:38 UTC  
> Url: https://github.com/boostorg/regex/issues/73  

I'm used to doing a ```man -s 3 srqt``` and getting linker info ...  
```  
SQRT(3)                                             Linux Programmer's Manual  
  
NAME  
       sqrt, sqrtf, sqrtl - square root function  
  
SYNOPSIS  
       #include <math.h>  
  
       double sqrt(double x);  
       float sqrtf(float x);  
       long double sqrtl(long double x);  
  
       Link with -lm.  
```  
  
When I go to the boost::regex docs (https://www.boost.org/doc/libs/1_66_0/libs/regex/doc/html/index.html) there is no such information: **Link with -lboost_regex**  
I think this is an oversight that should be corrected.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-09-19 21:11:09 UTC  
> Url: https://github.com/boostorg/regex/issues/73#issuecomment-422959925  

AMDG  
  
This should be assigned to boostorg/regex.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created at: 2018-09-27 22:27:15 UTC  
> Url: https://github.com/boostorg/regex/issues/73#issuecomment-425262463  

Even better, submit a PR into the boostorg/regex project fixing the documentation. :)

---

## Comment 3

> Username: komalbharadiya  
> Created at: 2018-12-18 11:54:52 UTC  
> Url: https://github.com/boostorg/regex/issues/73#issuecomment-448195849  

Hey @dtkerns , I am Komal from India. I am studying 4th year of engineering. I want to work on this issue. But I am totally new to open source, could you help me, as to how to go about this?

---

## Comment 4

> Username: jeking3  
> Created at: 2018-12-18 13:10:15 UTC  
> Url: https://github.com/boostorg/regex/issues/73#issuecomment-448215611  

Could a boostorg admin please move this issue to boostorg/regex?  Thanks.  
cc: @mclow, @mjcaisse, @pdimov (anyone an org admin?)
