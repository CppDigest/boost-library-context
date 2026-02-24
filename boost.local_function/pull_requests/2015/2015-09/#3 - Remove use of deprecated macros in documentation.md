# #3 Remove use of deprecated macros in documentation [Open]

> Username: mclow  
> Created at: 2015-09-11 23:07:05 UTC  
> Updated at: 2015-10-19 16:58:43 UTC  
> Url: https://github.com/boostorg/local_function/pull/3  

Replace uses in the documentation of the deprecated macro `BOOST_NO_LOCAL_CLASS_TEMPLATE_PARAMETERS` with `BOOST_NO_CXX11_LOCAL_CLASS_TEMPLATE_PARAMETERS` instead.  
  
The code already uses `BOOST_NO_CXX11_LOCAL_CLASS_TEMPLATE_PARAMETERS`, this is just a documentation change.   
  
I didn't touch the HTML files; I assume that they need to be regenerated from the QBK and checked in separately.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-10-13 16:39:45 UTC  
> Url: https://github.com/boostorg/local_function/pull/3#issuecomment-147771408  

Ping?

---

## Comment 2

> Username: lcaminiti  
> Created_at: 2015-10-13 21:17:37 UTC  
> Url: https://github.com/boostorg/local_function/pull/3#issuecomment-147856589  

Hi Marshall,  
  
Yes, QuickBook needs to be ran to re-generate the HTML and those need to be  
checked in Git. Also this macro should be changed if used in example/\* and  
test/\* code. The same of the NO_LAMBDA macro you mentioned before and for  
Boost.ScopeExit as well.  
  
I am hoping to have time to do this in the next couple of weeks (as I  
mentioned to you before, I was hoping to have some time early in Oct but I  
did not find time to do that yet).  
  
These are trivial changes to make... but I have been completely busy with a  
project a work for the last few month plus I have just moved to a new  
house. The work project paused last week and should not pick up again until  
next week so I will try to make these changes by then.  
  
Thanks.  
--Lorenzo  
  
On Tue, Oct 13, 2015 at 9:39 AM, Marshall Clow notifications@github.com  
wrote:  
  
> Ping?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/local_function/pull/3#issuecomment-147771408  
> .  
  
##   
  
--Lorenzo

---

## Comment 3

> Username: lcaminiti  
> Created_at: 2015-10-19 16:37:07 UTC  
> Url: https://github.com/boostorg/local_function/pull/3#issuecomment-149274672  

Hi Marshall,  
  
I have updated LocalFunction HTML docs. Do you want me to just commit those  
to master? (I also updated ScopeExit's HTML docs.)  
  
Thanks,  
--Lorenzo  
  
On Tue, Oct 13, 2015 at 9:39 AM, Marshall Clow notifications@github.com  
wrote:  
  
> Ping?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/local_function/pull/3#issuecomment-147771408  
> .  
  
##   
  
--Lorenzo

---

## Comment 4

> Username: mclow  
> Created_at: 2015-10-19 16:58:43 UTC  
> Url: https://github.com/boostorg/local_function/pull/3#issuecomment-149280387  

That would be great, thanks!

---
