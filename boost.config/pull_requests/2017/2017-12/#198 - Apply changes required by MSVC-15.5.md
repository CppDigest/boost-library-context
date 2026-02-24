# #198 Apply changes required by MSVC-15.5. [Merged]

> Username: jzmaddock  
> Created at: 2017-12-09 18:09:46 UTC  
> Updated at: 2019-11-02 11:17:12 UTC  
> Merged at: 2017-12-12 10:00:24 UTC  
> Closed at: 2017-12-12 10:00:24 UTC  
> Url: https://github.com/boostorg/config/pull/198  



---

## Comment 1

> Username: pdimov  
> Created_at: 2017-12-09 18:32:45 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-350496463  

By default (that is, without `permissive-`) I get failures in `config_test` because of two-phase lookup.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-12-10 09:33:53 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-350535887  

I thought /permissive- was the default for all new projects?  The appveyor tests pass.  
  
In any case we need a solution, and I don't see an easy one as there appears to be no way of detecting /permissive-  ?  I do note that nothing in Boost is using BOOST_NO_TWO_PHASE_NAME_LOOKUP at present so maybe this is moot?

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-12-10 13:24:52 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-350548236  

New IDE projects are one thing, but in this way you're going to get test failures in the matrix. Appveyor passes because you test with `/permissive-` there.

---

## Comment 4

> Username: OlafvdSpek  
> Created_at: 2017-12-11 13:38:20 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-350726331  

> Config.MSVC-14.5: complete configuration of new compiler features.  
  
15.5?

---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2017-12-11 19:57:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/198#pullrequestreview-82618922  

📁 test/config_info.cpp

```diff
 155 |+   PRINT_MACRO(_CPPUNWIND);
 156 |   PRINT_MACRO(_DLL);
 157 |+   PRINT_MACRO(_ISO_VOLATIL);
```

> Username: pdimov  
> Created_at: 2017-12-11 19:57:34 UTC  
> Updated_at: 2017-12-11 20:02:45 UTC  
> Url: https://github.com/boostorg/config/pull/198#discussion_r156182473  

Typo? Shouldn't this be `_ISO_VOLATILE`?


---

## Comment 6

> Username: jzmaddock  
> Created_at: 2017-12-12 08:53:13 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-350985583  

Just out of curiosity, did my replies to your comments (thanks!) come though, or has Github junked them as I don't see them here?

---

## Comment 7

> Username: OlafvdSpek  
> Created_at: 2017-12-12 09:08:51 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-350989531  

Mine? I saw commits but no replies I think.

---

## Comment 8

> Username: OlafvdSpek  
> Created_at: 2017-12-12 09:33:06 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-350995801  

BTW, is this targeted for 1.66.0, 1.66.1 or 1.67?

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2017-12-12 09:59:50 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-351002861  

>Mine? I saw commits but no replies I think.  
  
OK, not getting through then :(  
  
>BTW, is this targeted for 1.66.0, 1.66.1 or 1.67?  
  
Whatever comes after 1.66...

---

## Comment 10

> Username: s-martin  
> Created_at: 2017-12-15 09:28:09 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-351957103  

As Microsoft seems to release new compiler versions in much shorter intervals now, are there any ideas yet, how this can be covered by Boost releases?  
  
Are there any workarounds documented for Boost users to overcome the "Unknown compiler" messages, while there is no Boost release, which covers this?

---

## Comment 11

> Username: danieljames  
> Created_at: 2017-12-15 09:44:05 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-351960765  

It should say in the warning message, "Define BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE to suppress this message".

---

## Comment 12

> Username: s-martin  
> Created_at: 2017-12-15 11:35:34 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-351984598  

@danieljames it doesn't, but thanks for the info. I'lll check it out.

---

## Comment 13

> Username: danieljames  
> Created_at: 2017-12-15 11:50:14 UTC  
> Url: https://github.com/boostorg/config/pull/198#issuecomment-351987325  

Sorry, that message is added in the new release, coming soon hopefully.

---
