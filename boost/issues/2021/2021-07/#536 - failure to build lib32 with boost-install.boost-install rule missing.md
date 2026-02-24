# #536 - failure to build lib32 with boost-install.boost-install rule missing [Open]

> Username: cleinias  
> Created at: 2021-07-15 22:11:38 UTC  
> Updated at: 2021-07-16 16:37:01 UTC  
> Url: https://github.com/boostorg/boost/issues/536  

On my Archlinux system, the package lib32-boost, which pulls the code directly from boostorg's github repo, fails to build with the following error:  
    
ERROR: rule "boost-install.boost-install" unknown in module "Jamfile</home/stefano/builds/lib32-boost-libs/src/boost_1_76_0>".  
libs/atomic/build/Jamfile.v2:130: in modules.load  
  
  
I reported the problem to the Archlinux's package maintainer, and he suggested I reported upstream as well, as all the package does is to build boost with the 32 bits flag. Complete output form the build command is  [in this pastebin](https://pastebin.com/pwMKkYrEl)

---

## Comment 1

> Username: mclow  
> Created at: 2021-07-15 23:07:29 UTC  
> Url: https://github.com/boostorg/boost/issues/536#issuecomment-881065334  

The pastebin link goes to an empty page.

---

## Comment 2

> Username: cleinias  
> Created at: 2021-07-16 16:37:01 UTC  
> Url: https://github.com/boostorg/boost/issues/536#issuecomment-881576083  

Hmm, it worked when I tested the link.  [Here it is again](https://pastebin.com/pwMKkYrE)
