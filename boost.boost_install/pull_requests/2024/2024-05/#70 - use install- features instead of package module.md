# #70 use install- features instead of package module [Merged]

> Username: grisumbras  
> Created at: 2024-05-16 20:32:13 UTC  
> Updated at: 2024-06-12 13:34:29 UTC  
> Merged at: 2024-06-12 12:51:27 UTC  
> Closed at: 2024-06-12 12:51:27 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70  

The core of this change is swithcing from using the `package` module to `install-` features. This potentially allows property-set-based install prefixes and proper staging. In addition, there's some simplification done by switching from `generate` targets to `make` targets. Also, all helper features are removed and `<flags>` is used instead.  
  
PS-based installation and proper staging doesn't work _yet_ because `/boost/headers//install` doesn't use `install-` features.  
  
One note: most `install-X` features don't check corresponding `--X` options. Only `install-prefix` does. This is probably an oversight, so I should change that in b2 project.

---

## Comment 1

> Username: grisumbras  
> Created_at: 2024-05-20 15:30:25 UTC  
> Updated_at: 2024-05-20 19:10:29 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2120694047  

Update: bfgroup/b2#392 makes `install-X` features check `--X` options.

---

## Comment 2

> Username: grisumbras  
> Created_at: 2024-06-12 11:35:58 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162786693  

Related issues: #34, #46. Both should be fixed if this and boostorg/headers#1 are merged.

---

## Comment 3

> Username: grisumbras  
> Created_at: 2024-06-12 11:43:43 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162800100  

#32: while writing this PR I also wondered why DLLs aren't installed into bindir.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-06-12 12:14:02 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162857831  

They are, but only on Cygwin for some reason.  
  
Is this ready to merge at this point, replicating the old behavior 1:1?

---

## Comment 5

> Username: grisumbras  
> Created_at: 2024-06-12 12:38:28 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162904132  

Should be. It does work on my machine :tm:

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-06-12 12:52:32 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162935755  

To move DLLs into bindir, we'll probably have to introduce dlldir so that people have a way to get the old behavior back.

---

## Comment 7

> Username: pdimov  
> Created_at: 2024-06-12 13:12:01 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162976574  

```  
mkdir: cannot create directory ‘/usr/local/lib/cmake’: Permission denied  
```

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-06-12 13:12:30 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162977581  

Why did this stop working? The PR run passed.

---

## Comment 9

> Username: pdimov  
> Created_at: 2024-06-12 13:15:04 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162982850  

Maybe I needed to merge the headers PR as well?

---

## Comment 10

> Username: grisumbras  
> Created_at: 2024-06-12 13:22:20 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2162999809  

The headers PR shouldn't be necessary. I'll re-push and check.

---

## Comment 11

> Username: grisumbras  
> Created_at: 2024-06-12 13:34:28 UTC  
> Url: https://github.com/boostorg/boost_install/pull/70#issuecomment-2163026626  

Ok, something else definitely broke. I'll investigate.

---
