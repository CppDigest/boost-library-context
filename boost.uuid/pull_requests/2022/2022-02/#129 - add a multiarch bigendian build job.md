# #129 add a multiarch bigendian build job [Closed]

> Username: jeking3  
> Created at: 2022-02-01 23:27:48 UTC  
> Updated at: 2022-02-03 01:59:30 UTC  
> Closed at: 2022-02-02 22:33:52 UTC  
> Url: https://github.com/boostorg/uuid/pull/129  

Some code had trouble with endianness in the past; we used to run a bigendian job in Travis so this restores it.  Specifically, the MD5 code (and the name generator, which uses it) have endian branches.  
  
NOTE: this pull request disables all the other test jobs to speed development of the solution; the final will have all the jobs again.

---

## Comment 1

> Username: jeking3  
> Created_at: 2022-02-02 22:33:52 UTC  
> Url: https://github.com/boostorg/uuid/pull/129#issuecomment-1028421805  

Will open again once the boost-ci changes go in to make this easier.

---
