# #77 default install-package to Boost [Merged]

> Username: grisumbras  
> Created at: 2024-09-10 09:44:22 UTC  
> Updated at: 2024-09-11 20:05:06 UTC  
> Merged at: 2024-09-10 13:44:44 UTC  
> Closed at: 2024-09-10 13:44:44 UTC  
> Url: https://github.com/boostorg/boost_install/pull/77  

`<install-prefix>` on Windows defaults to `C:\<install-package>`. `<install-package>` defaults to project's id if it has one, otherwise to the first id of an ancestor project. Before modular changes this resulted in libs/Jamfile inheriting project id of boost root (boost) and default installation prefix on Windows was `C:\boost`. But with modular changes library projects have their ids, and install into `C:\<lib>` instead. This change tells projects that by default they should install to `C:\Boost`.

---
