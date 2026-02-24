# #221 - Does not work in the project c++ cli (0xc000007b) [Closed]

> Username: jenokizm  
> Created at: 2018-05-13 16:09:21 UTC  
> Updated at: 2018-05-18 06:46:49 UTC  
> Closed at: 2018-05-18 06:46:49 UTC  
> Url: https://github.com/boostorg/thread/issues/221  



---

## Comment 1

> Username: jenokizm  
> Created at: 2018-05-13 16:11:43 UTC  
> Updated at: 2018-05-13 16:12:46 UTC  
> Url: https://github.com/boostorg/thread/issues/221#issuecomment-388637939  

The same version of the library tested in a normal project, works. But when moved code to a project С++ CLI WinForms when starting application showing messangebox error 0xc000007b.   
If  
`#include <boost/thread.hpp>`  
Get the error "Error starting application 0xc000007b"  
![ashampoo_snap_2018 05 13_16h49m09s_001_](https://user-images.githubusercontent.com/11296510/39969209-7755189e-56e1-11e8-939e-892163022985.png)  
  
vs2017 Here my project source and exe https://yadi.sk/d/N3JcxPzd3Vpudf

---

## Comment 2

> Username: viboes  
> Created at: 2018-05-16 20:52:05 UTC  
> Url: https://github.com/boostorg/thread/issues/221#issuecomment-389661578  

Any PR fixing this configuration is welcome. I don't have access to Windows, neither the knowledge.

---

## Comment 3

> Username: jenokizm  
> Created at: 2018-05-16 21:00:25 UTC  
> Updated at: 2018-05-16 21:00:56 UTC  
> Url: https://github.com/boostorg/thread/issues/221#issuecomment-389664031  

In fact, I read that the CLI does not support even the usual std::threads, your library repels from them so it can not work vprintsype. So there's nothing to fix, except how to push Microsoft to add threads to the CLI...  
  
I think this topic can be closed
