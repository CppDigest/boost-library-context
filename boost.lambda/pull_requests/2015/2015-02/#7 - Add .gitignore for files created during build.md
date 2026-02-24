# #7 Add .gitignore for files created during build [Closed]

> Username: KayEss  
> Created at: 2015-02-24 03:00:19 UTC  
> Updated at: 2015-05-18 22:50:18 UTC  
> Closed at: 2015-05-18 22:50:18 UTC  
> Url: https://github.com/boostorg/lambda/pull/7  

There are some .dep files that end up in the test folder under certain circumstances. This adds an ignore for them.  
  
This keeps boost.lambda clean when it is used as a submodule in other projects.

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-05-18 22:50:18 UTC  
> Url: https://github.com/boostorg/lambda/pull/7#issuecomment-103240537  

There are way to many commits here. The technique to be used for a single pull request is to do a rebase of your change on the current 'develop' branch and then update your fork with just that change before you create a pull request with your fork.  
  
Please try again.

---
