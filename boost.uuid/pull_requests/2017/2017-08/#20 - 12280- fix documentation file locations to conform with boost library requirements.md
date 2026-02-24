# #20 12280: fix documentation file locations to conform with boost library requirements [Merged]

> Username: jeking3  
> Created at: 2017-08-10 15:08:23 UTC  
> Updated at: 2018-07-04 18:47:30 UTC  
> Merged at: 2017-08-10 15:45:13 UTC  
> Closed at: 2017-08-10 15:45:13 UTC  
> Url: https://github.com/boostorg/uuid/pull/20  

From 12280:  
  
I would recommend that we follow the pattern used in the boost/thread module, where boost/uuid/index.html would become a redirect to doc/index.html, and both index.html and uuid.html would move into boost/uuid/doc. Would this be sufficient to bring it in line with the style of the other modules?  
  
This is what I did.  
  
@grafikrobot would you be able to review this change so we know it meets the requirements you put into Trac 12280?  Thanks.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2017-08-10 15:12:02 UTC  
> Url: https://github.com/boostorg/uuid/pull/20#issuecomment-321581177  

@jeking3 Looks OK to me.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-20 20:24:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/20#issuecomment-338313520  

@grafikrobot is the same change needed in boost::format?  If so I can add an issue to it.  Let me know.

---
