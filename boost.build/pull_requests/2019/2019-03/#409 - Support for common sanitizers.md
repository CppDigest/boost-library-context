# #409 Support for common sanitizers [Merged]

> Username: djarek  
> Created at: 2019-03-10 23:01:55 UTC  
> Updated at: 2021-10-02 21:14:00 UTC  
> Merged at: 2019-04-21 22:30:26 UTC  
> Closed at: 2019-04-21 22:30:26 UTC  
> Url: https://github.com/boostorg/build/pull/409  

Add support for a common set of sanitizers supported by both gcc and clang:  
- thread  
- address  
- undefined

---

## Comment 1

> Username: mclow  
> Created_at: 2019-03-10 23:51:00 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-471366818  

I think having an example in the docs will make this much better.

---

## Comment 2

> Username: mclow  
> Created_at: 2019-03-11 02:02:55 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-471380287  

Thanks! Looks good to me.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2019-03-11 02:11:02 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-471381361  

AMDG  
  
- Please don't use a '-' in a feature value.  `-`  
  is used to separate subfeatures.  
- Since the feature is optional, you don't need  
  an `off` value.  The absence of the feature  
  means `off`.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-03-17 15:50:07 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-473678326  

The thing is that you can supply multiple sanitizes like `-fsanitize=address,undefined`, but the proposed method will not work for multiple sanitizes because `-fsanitize=address -fsanitize=undefined` is not allowed.

---

## Comment 5

> Username: djarek  
> Created_at: 2019-03-17 16:05:41 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-473679780  

@Kojoley you sure about that? Both clang and gcc seem to be fine with `-fsanitize=address -fsanitize=undefined`.

---

## Comment 6

> Username: djarek  
> Created_at: 2019-03-24 17:35:39 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-475981435  

@swatanabe   
Anything else to fix?

---

## Comment 7

> Username: Kojoley  
> Created_at: 2019-03-24 17:40:00 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-475981824  

> @Kojoley you sure about that? Both clang and gcc seem to be fine with -fsanitize=address -fsanitize=undefined.  
  
It seems I was wrong. Probably I tried to use other set of flags or it was fixed.

---

## Review 8 [Commented]

> Username: Kojoley  
> Created_at: 2019-03-24 17:44:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/409#pullrequestreview-218093171  

📁 src/tools/gcc.jam

```diff
 880 |+ toolset.flags gcc.link OPTIONS <address-sanitizer>norecover : -fsanitize=address -fno-sanitize-recover=address ;
 881 |+ toolset.flags gcc.link OPTIONS <undefined-sanitizer>norecover : -fsanitize=undefined -fno-sanitize-recover=undefined ;
 882 |+ toolset.flags gcc.link OPTIONS <thread-sanitizer>norecover : -fsanitize=thread -fno-sanitize-recover=thread ;
```

> Username: Kojoley  
> Created_at: 2019-03-24 17:44:21 UTC  
> Url: https://github.com/boostorg/build/pull/409#discussion_r268444512  

IIRC `-fno-sanitize-recover` is not a liker flag, but compile flag.


---

## Comment 9

> Username: grafikrobot  
> Created_at: 2019-04-16 01:39:46 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-483478197  

Is there more to do on this?

---

## Comment 10

> Username: djarek  
> Created_at: 2019-04-21 22:05:53 UTC  
> Url: https://github.com/boostorg/build/pull/409#issuecomment-485285661  

@grafikrobot I think there's nothing left to change.

---

## Review 11 [Commented]

> Username: jeking3  
> Created_at: 2019-05-13 11:30:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/409#pullrequestreview-236619692  

📁 src/tools/gcc.jam

```diff
 733 |+ toolset.flags gcc.compile.c++ OPTIONS <thread-sanitizer>on : -fsanitize=thread ;
 734 |+ toolset.flags gcc.compile.c++ OPTIONS <address-sanitizer>norecover : -fsanitize=address -fno-sanitize-recover=address ;
 735 |+ toolset.flags gcc.compile.c++ OPTIONS <undefined-sanitizer>norecover : -fsanitize=undefined -fno-sanitize-recover=undefined ;
```

> Username: jeking3  
> Created_at: 2019-05-13 11:30:11 UTC  
> Updated_at: 2019-05-13 11:30:12 UTC  
> Url: https://github.com/boostorg/build/pull/409#discussion_r283301665  

The description says "for gcc and clang", so... per clang documentation on this issue:  
  
https://clang.llvm.org/docs/AddressSanitizer.html  
  
To get a reasonable performance add -O1 or higher. To get nicer stack traces in error messages add -fno-omit-frame-pointer. To get perfect stack traces you may need to disable inlining (just use -O1) and tail call elimination (-fno-optimize-sibling-calls).  
  
I don't ususally add -O1, but I do add -g and -fno-omit-frame-pointer, and never tried -fno-optimize-sibling-calls.


---

## Review 12 [Commented]

> Username: jeking3  
> Created_at: 2019-05-13 12:38:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/409#pullrequestreview-236648724  

📁 src/tools/gcc.jam

```diff
 733 |+ toolset.flags gcc.compile.c++ OPTIONS <thread-sanitizer>on : -fsanitize=thread ;
 734 |+ toolset.flags gcc.compile.c++ OPTIONS <address-sanitizer>norecover : -fsanitize=address -fno-sanitize-recover=address ;
 735 |+ toolset.flags gcc.compile.c++ OPTIONS <undefined-sanitizer>norecover : -fsanitize=undefined -fno-sanitize-recover=undefined ;
```

> Username: jeking3  
> Created_at: 2019-05-13 12:38:21 UTC  
> Url: https://github.com/boostorg/build/pull/409#discussion_r283324513  

It also helps to define the environment variable UBSAN_OPTIONS=print_stacktrace=1 when running with the sanitizers, at least with the undefined one; I'm not sure this applies to other ones.


---
