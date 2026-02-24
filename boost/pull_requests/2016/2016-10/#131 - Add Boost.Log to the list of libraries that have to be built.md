# #131 Add Boost.Log to the list of libraries that have to be built. [Merged]

> Username: Lastique  
> Created at: 2016-10-02 19:37:01 UTC  
> Updated at: 2016-10-02 22:09:49 UTC  
> Merged at: 2016-10-02 22:00:57 UTC  
> Closed at: 2016-10-02 22:00:57 UTC  
> Url: https://github.com/boostorg/boost/pull/131  



---

## Comment 1

> Username: danieljames  
> Created_at: 2016-10-02 19:45:07 UTC  
> Url: https://github.com/boostorg/boost/pull/131#issuecomment-250991114  

Those pages are generated from the rst files, so you need to add it to the appropriate rst file, or it'll be overwritten next time the documentation is rebuilt.  
  
Although I expect that list is missing other libraries as no one is keeping it up to date, so I'd rather just remove it.

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-10-02 22:00:58 UTC  
> Url: https://github.com/boostorg/boost/pull/131#issuecomment-250999136  

On 10/02/16 22:45, Daniel James wrote:  
  
> Those pages are generated from the rst files, so you need to add it to  
> the appropriate rst file, or it'll be overwritten next time the  
> documentation is rebuilt.  
  
I'm not familiar with the rst files syntax, but I've added Boost.Log   
there as well. I hope I did it correctly.  
  
> Although I expect that list is missing other libraries as no one is  
> keeping it up to date, so I'd rather just remove it.  
  
People are complaining about Boost.Log missing from the list   
(https://svn.boost.org/trac/boost/ticket/12491), so that's why I created   
the PR. I'm ok if you decide to remove the list. Feel free to close this   
PR in any case (by merging it and keeping the list for now, or by not   
merging it and removing the list).

---

## Comment 3

> Username: danieljames  
> Created_at: 2016-10-02 22:09:49 UTC  
> Url: https://github.com/boostorg/boost/pull/131#issuecomment-250999567  

> > Although I expect that list is missing other libraries as no one is  
> >  keeping it up to date, so I'd rather just remove it.  
>   
> People are complaining about Boost.Log missing from the list  
> (https://svn.boost.org/trac/boost/ticket/12491), so that's why I created  
> the PR. I'm ok if you decide to remove the list. Feel free to close this  
> PR in any case (by merging it and keeping the list for now, or by not  
> merging it and removing the list).  
  
Sure, I was just thinking out loud about removing it, not expecting you to do so.

---
