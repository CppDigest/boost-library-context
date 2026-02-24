# #503 implement configurable installation prefixes that use features [Merged]

> Username: grisumbras  
> Created at: 2019-11-06 07:26:01 UTC  
> Updated at: 2021-10-02 21:01:52 UTC  
> Merged at: 2020-05-28 12:50:07 UTC  
> Closed at: 2020-05-28 12:50:07 UTC  
> Url: https://github.com/boostorg/build/pull/503  

This PR adds rule `add-install-dir` that registers a named installation directory that works similar to how those are [treated in Autotools](https://www.gnu.org/prep/standards/html_node/Directory-Variables.html). Only this one uses features instead of variables. As a result, one can do things like  
* request `b2 install-prefix=/usr install-bindir=/bin` to install executables into `/bin` and eveything else into `/usr`.  
* use conditional properties so that `b2 address-model=32,64` puts 32 bit libraries into `/usr/lib/i686-pc-linux-gnu`, and 64 bit libraries into `/usr/lib/x86_64-pc-linux-gnu`.  
  
By default every named directory supported by Autotools is added.  
  
Also, feature `<install-package>` is added to control target's package name (used on Windows and for directories like `docdir`). If corresponding property isn't present in property set, the package name is guessed from the project's id or directory name.  
  
Another feature `<staging-prefix>` is added to allow putting installed files into a different directory hierarchy (similar to Make's `destdir`).  
  
Finally, two rules are added that allow getting the path of a named directory and package name for a given property set. This allows generating configuration files based on active properties.  
  
All user-facing rules and features are documented.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2020-03-01 14:41:53 UTC  
> Url: https://github.com/boostorg/build/pull/503#issuecomment-593104726  

Are you still planning to complete this?

---

## Comment 2

> Username: grisumbras  
> Created_at: 2020-03-01 16:14:34 UTC  
> Url: https://github.com/boostorg/build/pull/503#issuecomment-593114565  

I do, though I haven't had much time recently.

---

## Review 3 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-05-27 17:20:23 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/503#pullrequestreview-419442371  

📁 src/tools/stage.jam

```diff
  38 | 
  39 | 
  40 |+ #| tag::features-doc[]
```

> Username: grafikrobot  
> Created_at: 2020-05-27 17:15:02 UTC  
> Updated_at: 2020-05-27 17:20:23 UTC  
> Url: https://github.com/boostorg/build/pull/503#discussion_r431309397  

Use a tag that reflects the feature name. So that it's easy to tell in the include what it is.

---

📁 src/tools/stage.jam

```diff
 110 |+ 
 111 |+ 
 112 |+ #| tag::features-doc[]
```

> Username: grafikrobot  
> Created_at: 2020-05-27 17:16:54 UTC  
> Updated_at: 2020-05-27 17:20:23 UTC  
> Url: https://github.com/boostorg/build/pull/503#discussion_r431310467  

Feature specific tag please.

---

📁 src/tools/stage.jam

```diff
 171 |+ 
 172 |+ 
 173 |+ #| tag::features2-doc[]
```

> Username: grafikrobot  
> Created_at: 2020-05-27 17:17:24 UTC  
> Updated_at: 2020-05-27 17:20:23 UTC  
> Url: https://github.com/boostorg/build/pull/503#discussion_r431310793  

Feature specific tag please.


---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:52 UTC  
> Url: https://github.com/boostorg/build/pull/503#issuecomment-932819979  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
