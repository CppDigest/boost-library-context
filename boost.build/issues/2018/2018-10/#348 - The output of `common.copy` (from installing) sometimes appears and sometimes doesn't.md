# #348 - The output of `common.copy` (from installing) sometimes appears and sometimes doesn't [Closed]

> Username: pdimov  
> Created at: 2018-10-05 13:02:54 UTC  
> Updated at: 2018-10-11 17:30:17 UTC  
> Closed at: 2018-10-11 17:30:16 UTC  
> Url: https://github.com/boostorg/build/issues/348  

Why is that, and can it be controlled?

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-10-11 17:16:55 UTC  
> Url: https://github.com/boostorg/build/issues/348#issuecomment-429041661  

AMDG  
  
On 10/05/2018 07:02 AM, Peter Dimov wrote:  
> Why is that, and can it be controlled?  
>   
  
By output, do you mean:  
     1 file(s) copied.  
  
I'm not aware of anything that should suppress this.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-11 17:30:16 UTC  
> Url: https://github.com/boostorg/build/issues/348#issuecomment-429046404  

My mistake. The actions of mine that don't appear use the `generate` rule to create the file directly in its installed place, not the `install` rule. So it's my `generate` actions that are silent, not `common.copy` (which is in fact overly verbose when it comes to installing the headers. :-) )
