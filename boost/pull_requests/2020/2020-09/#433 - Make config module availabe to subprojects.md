# #433 Make config module availabe to subprojects [Merged]

> Username: Flamefire  
> Created at: 2020-09-24 14:49:46 UTC  
> Updated at: 2020-10-09 14:20:04 UTC  
> Merged at: 2020-10-08 21:20:01 UTC  
> Closed at: 2020-10-08 21:20:02 UTC  
> Url: https://github.com/boostorg/boost/pull/433  

This makes it possible to `import config` instead of using a relative path requiring package managers to patch the files.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-09-24 14:59:17 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boost/pull/433#pullrequestreview-495665570  

📁 Jamroot

```diff
 293 | 
 294 |+ # Allow subprojects to simply `import config : requires ;` to get access to the requires rule
 295 |+ modules.poke : BOOST_BUILD_PATH : $(BOOST_ROOT)/libs/config/checks [ modules.peek : BOOST_BUILD_PATH ] ;
```

> Username: grafikrobot  
> Created_at: 2020-09-24 14:59:08 UTC  
> Updated_at: 2020-09-24 15:35:08 UTC  
> Url: https://github.com/boostorg/boost/pull/433#discussion_r494389581  

You actually want to move this up to before any `use-project`, but after the `BOOST_ROOT` var is specified.

> Username: Flamefire  
> Created_at: 2020-09-24 15:08:43 UTC  
> Updated_at: 2020-09-24 15:35:08 UTC  
> Url: https://github.com/boostorg/boost/pull/433#discussion_r494396776  

Done. But why? E.g. the `boost-install` rule is defined after those and it works. I guess `use-project` already imports the stuff and hence needs this?  
Is https://github.com/boostorg/boost/pull/433/commits/0fe1443100c6435e63eb5a3b26ece88aeb256a28 enough or still further up? Wanted to keep it close to the other `use-project` as it seems kinda related to me


---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-09-24 15:29:25 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boost/pull/433#pullrequestreview-495695617  

There's a use-project all the way up in line 152. So I think it has to move way up there.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2020-09-24 15:35:26 UTC  
> Url: https://github.com/boostorg/boost/pull/433#issuecomment-698422290  

Ok thanks, done that. Had to remove the previous commit which was faulty

---
