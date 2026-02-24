# #421 - Avoiding slowness for git submodule [Open]

> Username: saki7  
> Created at: 2020-08-14 08:57:37 UTC  
> Updated at: 2020-08-14 08:57:37 UTC  
> Url: https://github.com/boostorg/boost/issues/421  

I've recently started adding `boostorg/boost` as a git submodule for my own application. After experimenting, I've discovered that doing so will make your `git status` commands very slow (here ~3secs; normally ~0.2secs).  
  
> Note that your root repo will always look for your submodules' changes to keep everything in track, so the git command outside the boost submodule directory will also be affected  
  
git has the option [`submodule.<name>.ignore`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-submoduleltnamegtignore) for mitigating this kind of situation. I've modified my `.gitmodules` and confirmed it's working.  
  
__before:__  
```gitconfig  
[submodule "modules/boost"]  
        path = modules/boost  
        url = https://github.com/boostorg/boost.git  
```  
  
__after:__  
```gitconfig  
[submodule "modules/boost"]  
        path = modules/boost  
        url = https://github.com/boostorg/boost.git  
        ignore = all  
```  
  
I think this change has no disadvantages to most of the application developers, since they won't modify the contents of boostorg/boost itself during the development of their own program.  
  
Could someone (with write permission) add some notes to the wiki describing the workaround mentioned above?   
<https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview#installing-boost>
