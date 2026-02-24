# #155 - Feature Request: trash bin support [Closed]

> Username: zgpio  
> Created at: 2020-07-27 02:52:17 UTC  
> Updated at: 2020-07-27 10:00:50 UTC  
> Closed at: 2020-07-27 10:00:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/155  

I wonder if there are plans to support trash bin, or why not support it.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-07-27 10:00:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/155#issuecomment-664253369  

There are no plans to implement this. Trash bin is a feature of a Desktop Environment rather that a filesystem. Not every DE supports that feature and not every system has a DE to begin with, so the feature is not universally available. I imagine, for some DEs supporting this feature would require unreasonable dependencies for Boost.Filesystem. I think, this feature needs to be supported at a higher level than Boost.Filesystem.
