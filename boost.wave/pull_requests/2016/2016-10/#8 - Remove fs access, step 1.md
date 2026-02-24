# #8 Remove fs access, step 1 [Closed]

> Username: hia3  
> Created at: 2016-10-05 17:16:14 UTC  
> Updated at: 2016-10-05 20:25:44 UTC  
> Closed at: 2016-10-05 20:25:44 UTC  
> Url: https://github.com/boostorg/wave/pull/8  

Every called of include_paths::set_current_directory knows that path  
argument is a name of a file (not a directory name).  
  
Please don't apply this patch.

---

## Comment 1

> Username: hkaiser  
> Created_at: 2016-10-05 18:26:10 UTC  
> Url: https://github.com/boostorg/wave/pull/8#issuecomment-251757960  

What should I do with this PR if not merging it with the code base?

---

## Comment 2

> Username: hia3  
> Created_at: 2016-10-05 19:07:56 UTC  
> Url: https://github.com/boostorg/wave/pull/8#issuecomment-251769242  

This is just for illustration that every caller is passing `false` (meaning "not a directory") in all cases. You can disagree with some (all) of these cases.   
  
My plan was to replace some of these "false"s with a call to default_preprocessing_hooks::is_directory (to be able to redefine it later in client code). But none of them need this.  
  
Close it :-)

---
