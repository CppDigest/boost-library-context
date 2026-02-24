# #142 Remove API level checks in symlink functions in favor of runtime checks [Merged]

> Username: Flamefire  
> Created at: 2020-04-25 13:40:17 UTC  
> Updated at: 2020-05-01 15:01:43 UTC  
> Merged at: 2020-05-01 14:20:43 UTC  
> Closed at: 2020-05-01 14:20:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/142  

#100 became big and mixed unrelated things. So here is just the part making the symlink functions available if the runtime supports it. This is done by checking whether the functions exist (as before) and the compile time checks are not required. Only issue seen is `SYMBOLIC_LINK_FLAG_DIRECTORY` being not defined on low API levels, but as other implementations do: Just define it to the documented value for those rare cases (e.g. Cygwin using a pre XP default API level)

---

## Comment 1

> Username: Flamefire  
> Created_at: 2020-04-25 21:35:27 UTC  
> Url: https://github.com/boostorg/filesystem/pull/142#issuecomment-619443853  

@Lastique Ready :)

---
