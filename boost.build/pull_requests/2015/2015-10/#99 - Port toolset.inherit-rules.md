# #99 Port toolset.inherit-rules [Closed]

> Username: frenchtoast747  
> Created at: 2015-10-12 22:51:09 UTC  
> Updated at: 2021-10-02 22:20:15 UTC  
> Closed at: 2015-10-13 08:27:04 UTC  
> Url: https://github.com/boostorg/build/pull/99  

I'm not sure if this is the best way to handle this or not, but I believe this emulates Jam rule overriding. I had to take out the exception that is raised if an action of the same name is registered so that a user can inherit actions and then override as necessary.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-10-13 08:27:04 UTC  
> Url: https://github.com/boostorg/build/pull/99#issuecomment-147646228  

Cherry-picked, thanks!

---
