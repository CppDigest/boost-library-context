# #116 Add support for Elbrus 2000 (e2k) architecture [Merged]

> Username: SSE4  
> Created at: 2021-01-29 10:22:19 UTC  
> Updated at: 2021-05-16 06:25:44 UTC  
> Merged at: 2021-02-10 04:52:29 UTC  
> Closed at: 2021-02-10 04:52:29 UTC  
> Url: https://github.com/boostorg/predef/pull/116  

/cc @grafikrobot

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2021-02-10 04:03:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/predef/pull/116#pullrequestreview-587231479  

📁 include/boost/predef/architecture/e2k.h

```diff
   1 |+ /*
   2 |+ Copyright Rene Rivera 2008-2021
```

> Username: grafikrobot  
> Created_at: 2021-02-10 03:57:25 UTC  
> Updated_at: 2021-02-10 04:32:02 UTC  
> Url: https://github.com/boostorg/predef/pull/116#discussion_r573428848  

Can you assign yourself as copyright holder?


📁 include/boost/predef/architecture/sparc.h

```diff
  36 | #   undef BOOST_ARCH_SPARC
  35 |- #   if !defined(BOOST_ARCH_SPARC) && defined(__sparcv9)
  37 |+ #   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv9) || defined(__sparc_v9__)
```

> Username: grafikrobot  
> Created_at: 2021-02-10 04:03:03 UTC  
> Updated_at: 2021-02-10 04:32:02 UTC  
> Url: https://github.com/boostorg/predef/pull/116#discussion_r573430598  

Did you intend to include the `sparc.h` changes in this PR?

> Username: SSE4  
> Created_at: 2021-02-10 04:30:34 UTC  
> Updated_at: 2021-02-10 04:32:02 UTC  
> Url: https://github.com/boostorg/predef/pull/116#discussion_r573438157  

yes, that's from MCST compiler which is capable for e2k and sparc


---

## Review 2 [Commented]

> Username: SSE4  
> Created_at: 2021-02-10 04:31:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/predef/pull/116#pullrequestreview-587242045  

📁 include/boost/predef/architecture/e2k.h

```diff
   1 |+ /*
   2 |+ Copyright Rene Rivera 2008-2021
```

> Username: SSE4  
> Created_at: 2021-02-10 04:31:56 UTC  
> Updated_at: 2021-02-10 04:32:02 UTC  
> Url: https://github.com/boostorg/predef/pull/116#discussion_r573438469  

```diff  
-Copyright Rene Rivera 2008-2021  
+Copyright Konstantin Ivlev 2021  
```


---

## Comment 3

> Username: slonm  
> Created_at: 2021-05-16 06:25:44 UTC  
> Url: https://github.com/boostorg/predef/pull/116#issuecomment-841775862  

Compilation failed:  
```  
In file included from ./boost/predef/architecture.h:27:  
./boost/predef/architecture/sparc.h:37:82: error: expected ')' in preprocessor expression  
#   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv9) || defined(__sparc_v9__)  
                                      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
./boost/predef/architecture/sparc.h:37:38: note: to match this '('  
#   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv9) || defined(__sparc_v9__)  
                                     ^  
./boost/predef/architecture/sparc.h:40:82: error: expected ')' in preprocessor expression  
#   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv8) || defined(__sparc_v8__)  
                                      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
./boost/predef/architecture/sparc.h:40:38: note: to match this '('  
#   if !defined(BOOST_ARCH_SPARC) && (defined(__sparcv8) || defined(__sparc_v8__)  
                                     ^  
```

---
