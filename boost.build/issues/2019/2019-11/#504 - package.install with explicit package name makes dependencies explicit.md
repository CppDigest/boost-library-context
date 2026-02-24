# #504 - package.install with explicit package name makes dependencies explicit [Closed]

> Username: grisumbras  
> Created at: 2019-11-11 07:52:46 UTC  
> Updated at: 2019-11-14 18:25:24 UTC  
> Closed at: 2019-11-14 18:25:24 UTC  
> Url: https://github.com/boostorg/build/issues/504  

Consider this minimal jamfile:  
```  
import package ;  
  
exe foo : main.cpp ;  
package.install install foo : : foo ;  
```  
Invoking `b2` doesn't build `foo`, but invoking either `b2 foo` or `b2 install` does.  
  
Additionally, with  
```  
import package ;  
  
exe foo : main.cpp ;  
package.install install foo2 : : foo ;  
```  
invoking `b2` does build `foo`.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-11-11 13:22:19 UTC  
> Url: https://github.com/boostorg/build/issues/504#issuecomment-552443370  

Right, and? Only issue I see is that it's not an error to have both an `exe` and `install` targets have the same name. Or is there more to the sample code that I don't understand?

---

## Comment 2

> Username: grisumbras  
> Created at: 2019-11-11 13:32:02 UTC  
> Updated at: 2019-11-11 13:32:17 UTC  
> Url: https://github.com/boostorg/build/issues/504#issuecomment-552446876  

`package.install install foo : ... ;` does not create a target named `foo`, it creates a target named `install` (and a couple of targets like `install-bin` etc. The second string of the first argument (`foo` in this case) is only used for the default installation prefix on Windows.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2019-11-11 13:33:46 UTC  
> Url: https://github.com/boostorg/build/issues/504#issuecomment-552447466  

Oh, right.. brain skipped over the install first arg of `install` :-)
