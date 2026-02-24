# #174 Drone config [Merged]

> Username: sdarwin  
> Created at: 2020-12-29 18:03:26 UTC  
> Updated at: 2021-04-18 11:48:29 UTC  
> Merged at: 2021-04-18 11:48:29 UTC  
> Closed at: 2021-04-18 11:48:29 UTC  
> Url: https://github.com/boostorg/container/pull/174  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-01-12 20:50:35 UTC  
> Url: https://github.com/boostorg/container/pull/174#issuecomment-758967978  

Why does the framework need to have write permissions?  
  
"This application will be able to read and write all public repository data. This includes the following:  
  
Code  
Issues  
Pull requests  
Wikis  
Settings  
Webhooks and services  
Deploy keys"  
  
Why aren't read permissions enough?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-01-12 22:12:27 UTC  
> Updated_at: 2021-01-12 22:25:52 UTC  
> Url: https://github.com/boostorg/container/pull/174#issuecomment-759068930  

@igaztanaga, I definitely agree that it's less than ideal.    
  
By comparison, the main drone website https://cloud.drone.io/   Sign In -> Authorize cloud.drone.io requests the same thing.   And https://travis-ci.com/signup is worse, "This application will be able to read and write all public and **private** repository data."       
  
A CI app needs to be able to write webhooks, pull requests, and read the repositories.  Github has traditionally not offered fine grained permissions to deal with these requirements.

---
