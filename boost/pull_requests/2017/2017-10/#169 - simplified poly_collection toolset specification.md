# #169 simplified poly_collection toolset specification [Merged]

> Username: joaquintides  
> Created at: 2017-10-21 16:53:49 UTC  
> Updated at: 2017-10-25 12:57:40 UTC  
> Merged at: 2017-10-21 17:38:13 UTC  
> Closed at: 2017-10-21 17:38:13 UTC  
> Url: https://github.com/boostorg/boost/pull/169  



---

## Comment 1

> Username: viboes  
> Created_at: 2017-10-25 11:33:40 UTC  
> Url: https://github.com/boostorg/boost/pull/169#issuecomment-339300619  

I've never understood how we can filter with toolset names, so I'm surely missing somthing.  
Isn't the toolset something that the user choose in his user_config file?   
If it is the case, i'm not saying that it couldn't work for the specific tool set we are using today, but if I use a toolset that doesn't name it xxx98, how can you filter it?

---

## Comment 2

> Username: joaquintides  
> Created_at: 2017-10-25 12:57:40 UTC  
> Url: https://github.com/boostorg/boost/pull/169#issuecomment-339320996  

Hi Vicente,  
  
Honestly, I don't know :-) Seems to me this is more of a convention than a rigidly specified naming convention, so, yes, the whole thing is a bit fragile, but it's what we have.  
  
Disabling toolsets/environments through Jamfile requirements, on the other hand, has the potential to make this much more riguorous, plus it integrates nicely with other CI tools like Travis. Unfortunately noone has made this feature progress in the direction of providing space for report notes etc. Given the curent state of affairs wrt b2/Cmake I doubt this will ever happen.

---
