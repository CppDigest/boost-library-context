# #3 Develop [Closed]

> Username: mikehcox  
> Created at: 2013-12-03 08:37:03 UTC  
> Updated at: 2014-07-01 11:19:42 UTC  
> Closed at: 2014-01-22 12:26:22 UTC  
> Url: https://github.com/boostorg/boost/pull/3  

These changes are a superset of pull request #2.  In addition to the files and dirs in #2, it adds the generated jam files project-config.jam and user-config.jam.  Also, the ignored files and dirs are prefixed with a / so that those same files and dirs won't accidentally match something in a subdir of the boost repo.  
  
In addition to the generated files and dirs in boost, there are generated files and dirs in tools/build.  I placed a .gitignore file in that repos to ignore the appropriate files and dirs.  That is change is in a pull request for the build.git repo.  
  
I also noticed changes were made to libs/system and tools/litre didn't have corresponding submodule refs in the boost repo.  In addition, added a submodule ref to the boost repo for my changes in tools/build.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-01-22 12:26:22 UTC  
> Url: https://github.com/boostorg/boost/pull/3#issuecomment-33017134  

I have applied this, manually. Thanks!

---
