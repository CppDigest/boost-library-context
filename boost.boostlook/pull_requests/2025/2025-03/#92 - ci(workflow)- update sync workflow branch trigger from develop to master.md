# #92 ci(workflow): update sync workflow branch trigger from develop to master [Merged]

> Username: julioest  
> Created at: 2025-03-07 21:08:15 UTC  
> Updated at: 2025-03-07 23:16:55 UTC  
> Merged at: 2025-03-07 23:16:55 UTC  
> Closed at: 2025-03-07 23:16:55 UTC  
> Url: https://github.com/boostorg/boostlook/pull/92  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-03-07 21:21:25 UTC  
> Url: https://github.com/boostorg/boostlook/pull/92#issuecomment-2707483806  

An automated preview of the documentation is available at [https://92.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://92.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 2

> Username: sdarwin  
> Created_at: 2025-03-07 21:25:06 UTC  
> Url: https://github.com/boostorg/boostlook/pull/92#issuecomment-2707489989  

Looks good.  
  
We need to think about how this will interact with a script that quickly publishes:  
- boostlook  
- website-v2-docs  
- website-v2  
  
There are timing issues.       
`website-v2-docs` will be run due to this github action. And then also the script.

---
