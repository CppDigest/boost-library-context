# #523 bugfix: saxonhe_dir actions now conditional on build host OS [Merged]

> Username: madmongo1  
> Created at: 2019-12-29 17:25:22 UTC  
> Updated at: 2021-10-02 21:13:33 UTC  
> Merged at: 2020-01-20 20:04:02 UTC  
> Closed at: 2020-01-20 20:04:02 UTC  
> Url: https://github.com/boostorg/build/pull/523  

When I write the saxionhe_dir actions clause I did not realise that these actions are copied to the command line without interpretation with respect to the build host OS.  
  
As a result, I fixed beast documentation building for linux, but broke it for windows.  
  
This change fixes it for both.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-12-29 17:26:47 UTC  
> Url: https://github.com/boostorg/build/pull/523#issuecomment-569525390  

Suggested commit message:  
  
> saxonhe_dir works on Windows

---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2019-12-30 13:55:32 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/523#pullrequestreview-337111266  

📁 src/tools/saxonhe.jam

```diff
  37 | #
  37 |- actions saxonhe_dir
  38 |+ if [ os.name ] = NT
```

> Username: grafikrobot  
> Created_at: 2019-12-30 13:51:15 UTC  
> Updated_at: 2020-01-20 15:28:55 UTC  
> Url: https://github.com/boostorg/build/pull/523#discussion_r361990816  

Use `[ os.on-windows ]`.

> Username: madmongo1  
> Created_at: 2019-12-30 14:16:51 UTC  
> Updated_at: 2020-01-20 15:28:55 UTC  
> Url: https://github.com/boostorg/build/pull/523#discussion_r361996725  

Thanks for that. As you've probably guessed, I'm a bjam noob. Vinnie and I just copied what we saw elsewhere.

> Username: vinniefalco  
> Created_at: 2019-12-30 15:01:42 UTC  
> Updated_at: 2020-01-20 15:28:55 UTC  
> Url: https://github.com/boostorg/build/pull/523#discussion_r362008170  

@madmongo1 we should change it in all my Jamfiles (e.g. Beast, JSON, URL)

> Username: grafikrobot  
> Created_at: 2019-12-30 15:34:31 UTC  
> Updated_at: 2020-01-20 15:28:55 UTC  
> Url: https://github.com/boostorg/build/pull/523#discussion_r362017310  

@madmongo1 no problem :-) I consider things like this "expert" level since they aren't easy to find (lack of visible documentation).

---

📁 src/tools/saxonhe.jam

```diff
  40 |+     actions saxonhe_dir
  41 |+     {
  42 |+         MKDIR "$(<)"
```

> Username: grafikrobot  
> Created_at: 2019-12-30 13:55:23 UTC  
> Updated_at: 2020-01-20 15:28:55 UTC  
> Url: https://github.com/boostorg/build/pull/523#discussion_r361991736  

You might need to make this conditional on dir exists as per common.mkdir action: https://github.com/boostorg/build/blob/develop/src/tools/common.jam#L725

> Username: madmongo1  
> Created_at: 2019-12-30 14:17:38 UTC  
> Updated_at: 2020-01-20 15:28:55 UTC  
> Url: https://github.com/boostorg/build/pull/523#discussion_r361996926  

I don't suppose there's a way to call an action from an action, in order to make the jamfiles more DRY?

> Username: grafikrobot  
> Created_at: 2019-12-30 15:36:13 UTC  
> Updated_at: 2020-01-20 15:28:55 UTC  
> Url: https://github.com/boostorg/build/pull/523#discussion_r362017780  

Nothing direct at the moment. It could be done with some more "expert" level rule calling actions code. But this is definitely a feature that would be good to have :-)


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-01-20 20:05:21 UTC  
> Url: https://github.com/boostorg/build/pull/523#issuecomment-576414026  

doesn't this need to go into _master_ to fix the broken superproject build?

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2020-01-20 20:18:21 UTC  
> Url: https://github.com/boostorg/build/pull/523#issuecomment-576417990  

Yes. But as @pdimov mentioned that's only going to happen on another B2 release. Which will not be soon.

---
