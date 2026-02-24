# #280 move context-impl feature into a module [Merged]

> Username: grisumbras  
> Created at: 2025-01-07 13:06:21 UTC  
> Updated at: 2025-01-07 14:41:45 UTC  
> Merged at: 2025-01-07 14:39:38 UTC  
> Closed at: 2025-01-07 14:39:38 UTC  
> Url: https://github.com/boostorg/context/pull/280  

This is now necessary to allow users to set it from the command line.

---

## Comment 1

> Username: grisumbras  
> Created_at: 2025-01-07 13:09:07 UTC  
> Url: https://github.com/boostorg/context/pull/280#issuecomment-2575260467  

For context, a user reported this on Slack:  
  
```  
oipo@oipo-X670E-AORUS-MASTER:~/Programming/Ichor/build/boost_1_87_0$ ./b2 variant=debug link=static threading=multi context-impl=ucontext  
/home/oipo/Programming/Ichor/build/boost_1_87_0/tools/build/src/build/feature.jam:327: in validate-feature from module feature  
error: unknown feature "<context-impl>"  
/home/oipo/Programming/Ichor/build/boost_1_87_0/tools/build/src/build/feature.jam:361: in expand-subfeatures-aux from module feature  
/home/oipo/Programming/Ichor/build/boost_1_87_0/tools/build/src/build/feature.jam:422: in feature.expand-subfeatures from module feature  
/home/oipo/Programming/Ichor/build/boost_1_87_0/tools/build/src/build/build-request.jam:20: in apply-to-property-set from module build-request  
(builtin):-1: in sequence.transform from module sequence  
/home/oipo/Programming/Ichor/build/boost_1_87_0/tools/build/src/build/build-request.jam:32: in build-request.expand-no-defaults from module build-request  
/home/oipo/Programming/Ichor/build/boost_1_87_0/tools/build/src/build-system.jam:778: in module scope from module build-system  
```  
  
Essentially, after the modular changes all user-facing B2 features of a project have to be defined in that project's `build.jam`.

---
