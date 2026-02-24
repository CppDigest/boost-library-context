# #558 `<force-include>` feature [Merged]

> Username: Kojoley  
> Created at: 2020-03-28 01:01:53 UTC  
> Updated at: 2021-10-02 21:02:22 UTC  
> Merged at: 2020-04-02 16:56:48 UTC  
> Closed at: 2020-04-02 16:56:48 UTC  
> Url: https://github.com/boostorg/build/pull/558  

Closes #387  
  
Some nitpicks:  
  
1. `-include\ "$(FORCE_INCLUDES)"` does not work because despite of [documentation](https://boostorg.github.io/build/manual/develop/index.html#jam.language.lexical) a backslash does not escape a whitespace, used `-include"$(FORCE_INCLUDES)"` instead (and it surprisingly worked out).  
  
2. I did not add this to MSVC MIDL/RC or GCC Fortran as I am not sure if they need it.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-03-31 14:36:07 UTC  
> Url: https://github.com/boostorg/build/pull/558#issuecomment-606667388  

Any comments?

---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-04-02 00:56:26 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/558#pullrequestreview-386042408  

General.. Yeah the "\ " space thing just works differently inside actions.  
Not sure I like the "force-include" name. Other names I thought of: `pre-include` and `first-include`. Those say a bit more about the functionality.  
Otherwise, change looks useful.

📁 src/tools/features/force-include-feature.jam

```diff
   6 |+ import feature ;
   7 |+ 
   8 |+ #| tag::doc[]
```

> Username: grafikrobot  
> Created_at: 2020-04-02 00:46:36 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401991322  

For the docs you also need to add to the `reference.adoc` file.

> Username: Kojoley  
> Created_at: 2020-04-02 02:46:13 UTC  
> Updated_at: 2020-04-02 02:46:14 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r402021434  

Done

---

📁 src/tools/features/force-include-feature.jam

```diff
  10 |+ [[bbv2.builtin.features.force-include]]`force-include`::
  11 |+ Specifies an include path that has to be included in a way like if
  12 |+ `#include "file"` appeared as the first line of the primary source file.
```

> Username: grafikrobot  
> Created_at: 2020-04-02 00:47:15 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401991487  

Mention what happens when multiple ones are specified.

---

📁 src/tools/features/force-include-feature.jam

```diff
  16 |+ feature.feature "force-include"
  17 |+     :
  18 |+     : free #order-sensitive
```

> Username: grafikrobot  
> Created_at: 2020-04-02 00:47:30 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401991550  

Remove the commented out part.

> Username: Kojoley  
> Created_at: 2020-04-02 01:12:33 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401998237  

Does it even work? I copied it from `<include>` and I remember some discussions about making it working but as far as I know it is still not the case.

> Username: grafikrobot  
> Created_at: 2020-04-02 01:15:17 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401998920  

I meant literally delete the `#order-sesitive` text. No point in having it there if it's not doing anything.

> Username: Kojoley  
> Created_at: 2020-04-02 01:17:53 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401999548  

Ok...


📁 test/feature_force_include.py

```diff
  14 |+ t.write("Jamroot.jam", """
  15 |+ obj test-cpp : test.cpp : <force-include>test.h ;
  16 |+ obj test-c : test.c : <force-include>test.h ;
```

> Username: grafikrobot  
> Created_at: 2020-04-02 00:53:25 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401993113  

Could you also test when specifying multiple `<force-include>`.

> Username: Kojoley  
> Created_at: 2020-04-02 01:13:37 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401998495  

Would it be ok to test only multiple or I need to create another test?

> Username: grafikrobot  
> Created_at: 2020-04-02 01:15:35 UTC  
> Updated_at: 2020-04-02 01:53:39 UTC  
> Url: https://github.com/boostorg/build/pull/558#discussion_r401998993  

Sure, that's fine.


---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-04-02 01:17:13 UTC  
> Url: https://github.com/boostorg/build/pull/558#issuecomment-607567068  

> Not sure I like the "force-include" name. Other names I thought of: pre-include and first-include.  
  
I took the name from the MSVC. Maybe I like `pre-include` more, but it creates a false sense of it being before `include` while they are distinct things.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2020-04-02 01:32:00 UTC  
> Url: https://github.com/boostorg/build/pull/558#issuecomment-607570681  

> > Not sure I like the "force-include" name. Other names I thought of: pre-include and first-include.  
>   
> I took the name from the MSVC. Maybe I like `pre-include` more, but it creates a false sense of it being before `include` while they are distinct things.  
  
I guess force-include is fine. Having at least one compiler use the term is enough to make me think it's a reasonable term.

---

## Comment 5

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:02:21 UTC  
> Url: https://github.com/boostorg/build/pull/558#issuecomment-932820064  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
