# #188 posix/executor: Don't modify ::environ [Closed]

> Username: dkl  
> Created at: 2020-12-08 20:33:12 UTC  
> Updated at: 2021-10-14 06:40:23 UTC  
> Closed at: 2021-10-14 06:40:23 UTC  
> Url: https://github.com/boostorg/process/pull/188  

Modifying the PATH entry in the environ global variable causes future PATH variable lookups to fail. So do the same but without modifying ::environ (this is the solution originally suggested in issue #121).  
  
(Found due to failing cmd_test unit test since commit 5329519)

---
