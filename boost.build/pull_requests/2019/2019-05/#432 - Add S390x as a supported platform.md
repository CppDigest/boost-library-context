# #432 Add S390x as a supported platform [Merged]

> Username: nealef  
> Created at: 2019-05-01 13:55:29 UTC  
> Updated at: 2021-10-02 21:19:00 UTC  
> Merged at: 2019-05-01 15:52:34 UTC  
> Closed at: 2019-05-01 15:52:34 UTC  
> Url: https://github.com/boostorg/build/pull/432  

Add s390x system and its three latest instruction sets:  
  
* doc/src/reference.adoc  
    - Add s390x tag to list of supported archs  
* src/tools/builtin.py   
    - Add s390x architecture and instruction sets for latest 3 generations  
* src/tools/features/architecture-feature.jam  
    - Add s390x architecture and instruction sets for latest 3 generations  
* src/tools/gcc.jam  
    - Define flags used to select instruction sets  
* src/tools/gcc.py  
    - Define flags used to select instruction sets

---
