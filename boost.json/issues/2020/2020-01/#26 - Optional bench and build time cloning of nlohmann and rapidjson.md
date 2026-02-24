# #26 - Optional bench and build time cloning of nlohmann and rapidjson [Closed]

> Username: ayounes-synaptics  
> Created at: 2020-01-07 10:21:58 UTC  
> Updated at: 2020-08-27 04:34:34 UTC  
> Closed at: 2020-08-27 04:34:34 UTC  
> Url: https://github.com/boostorg/json/issues/26  

Hello,  
Thanks for this library, I find it very interesting, especially for deeply embedded platform (without OS).  
I feel that having nlohmann and rapidjson imposed as git submodule just for the purpose of benchmarking is too much. Full cloning of nlohmann in particular is quite big...  
What about moving that to build time retrieving thanks to cmake ExternalProject_Add?  
  
include(ExternalProject)  
ExternalProject_Add(bench/lib/nlohmann  
  GIT_REPOSITORY    https://github.com/nlohmann/json.git  
  GIT_TAG           99d7518d21cbbfe91d341a5431438bf7559c6974  
)  
  
Also compilation of bench should be optional.  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-07 13:53:20 UTC  
> Url: https://github.com/boostorg/json/issues/26#issuecomment-571595371  

I will look into it but remember that this is going to be proposed for Boost, and you will get the library through Boost, not through git. In that configuration there will be no submodule or benchmark program.

---

## Comment 2

> Username: ayounes-synaptics  
> Created at: 2020-01-07 14:37:02 UTC  
> Url: https://github.com/boostorg/json/issues/26#issuecomment-571613117  

Thanks.  
As a side note, just for information, I think a lot of people like the fact that this library can be used in standalone, independently from boost. Also Modularized boost is actually using git submodule and I guess it will use this git?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-01-07 14:49:05 UTC  
> Url: https://github.com/boostorg/json/issues/26#issuecomment-571618107  

> Modularized boost is actually using git submodule and I guess it will use this git?  
  
Ah, so some other project is doing a deep fetch of the submodules in my json lib?

---

## Comment 4

> Username: ayounes-synaptics  
> Created at: 2020-01-07 14:50:27 UTC  
> Url: https://github.com/boostorg/json/issues/26#issuecomment-571618718  

> > Modularized boost is actually using git submodule and I guess it will use this git?  
>   
> Ah, so some other project is doing a deep fetch of the submodules in my json lib?  
  
Exactly yes.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-01-07 14:51:15 UTC  
> Url: https://github.com/boostorg/json/issues/26#issuecomment-571619054  

Maybe I can just put a bash script there instead which clones the necessary repositories and specific commit ids, and remove the submodules.

---

## Comment 6

> Username: ayounes-synaptics  
> Created at: 2020-01-07 14:53:36 UTC  
> Url: https://github.com/boostorg/json/issues/26#issuecomment-571620062  

> Maybe I can just put a bash script there instead which clones the necessary repositories and specific commit ids, and remove the submodules.  
  
That would also work indeed. But ExternalProject_Add() will do that for you and even call cmake automatically at build time on the child project (if using cmake).

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-01-07 16:08:28 UTC  
> Url: https://github.com/boostorg/json/issues/26#issuecomment-571653324  

Oh... :) That looks very nice, I will try that.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-08-27 04:34:34 UTC  
> Url: https://github.com/boostorg/json/issues/26#issuecomment-681404665  

We went the bash script route, which places less burden on the CML file.
