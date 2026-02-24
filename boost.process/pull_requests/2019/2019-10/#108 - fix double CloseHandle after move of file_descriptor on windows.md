# #108 fix double CloseHandle after move of file_descriptor on windows [Closed]

> Username: ray-haeb  
> Created at: 2019-10-30 08:29:10 UTC  
> Updated at: 2019-11-22 05:09:12 UTC  
> Closed at: 2019-11-22 05:09:12 UTC  
> Url: https://github.com/boostorg/process/pull/108  

Like #106 but for branch develop

---

## Comment 1

> Username: ray-haeb  
> Created_at: 2019-10-30 09:22:00 UTC  
> Url: https://github.com/boostorg/process/pull/108#issuecomment-547809548  

The Travis build fails because of some invalid git submodule:  
  
> 93.81s$ git pull --recurse-submodules  
Fetching submodule libs/accumulators  
[...]  
Fetching submodule libs/beast  
Could not access submodule 'doc/docca'  
Fetching submodule libs/bimap  
Fetching submodule libs/bind  
[...]  
Fetching submodule tools/quickbook  
The command "git pull --recurse-submodules" failed and exited with 1 during .  
Your build has been stopped.

---
