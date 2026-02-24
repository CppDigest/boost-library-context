# #704 Spelling [Closed]

> Username: jsoref  
> Created at: 2022-11-06 22:03:26 UTC  
> Updated at: 2023-01-21 23:30:47 UTC  
> Closed at: 2023-01-21 22:12:03 UTC  
> Url: https://github.com/boostorg/boost/pull/704  

This PR corrects misspellings identified by the [check-spelling action](https://github.com/marketplace/actions/check-spelling).  
  
The misspellings have been reported at https://github.com/jsoref/boost/commit/2699db461dfa7e219f22eb961056f7321011715c#commitcomment-89062870  
  
The action reports that the changes in this PR would make it happy: https://github.com/jsoref/boost/commit/19643c51ade2a96ba08b53e259cd6f1737a175a2  
  
Note: this PR does not include the action. If you're interested in running a spell check on every PR and push, that can be offered separately.

---

## Review 1 [Commented]

> Username: jsoref  
> Created_at: 2022-11-06 22:05:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/704#pullrequestreview-1169658384  

Most corrections automatically suggested by Google Sheets. All fault mine.

📁 Jamroot

```diff
 249 | explicit-alias unit_test_framework : libs/test/build//boost_unit_test_framework ;
 250 |- explicit-alias bgl-vis : libs/graps/build//bgl-vis ;
 250 |+ explicit-alias bgl-vis : libs/graph/build//bgl-vis ;
```

> Username: jsoref  
> Created_at: 2022-11-06 22:03:37 UTC  
> Updated_at: 2022-11-06 22:05:45 UTC  
> Url: https://github.com/boostorg/boost/pull/704#discussion_r1014899919  

I can't figure this one out.


📁 boostcpp.jam

```diff
 637 |+ local deductible-architectures = arm mips1 power riscv s390x sparc x86 combined ;
 638 |+ feature.feature deduced-architecture : $(deductible-architectures) : propagated optional composite hidden ;
 639 |+ for a in $(deductible-architectures)
```

> Username: jsoref  
> Created_at: 2022-11-06 22:04:11 UTC  
> Updated_at: 2022-11-06 22:05:45 UTC  
> Url: https://github.com/boostorg/boost/pull/704#discussion_r1014899983  

Actually, this is probably `deducible`


📁 doc/test/gold/document_to_test_formatting/remez.html

```diff
 223 |       In single point exchange, we move the control point nearest to the largest
 224 |-       extrema to the absissa value of the extrema.
 224 |+       extrema to the abscissa value of the extrema.
```

> Username: jsoref  
> Created_at: 2022-11-06 22:04:30 UTC  
> Updated_at: 2022-11-06 22:05:45 UTC  
> Url: https://github.com/boostorg/boost/pull/704#discussion_r1014900020  

??


📁 libs/numeric/sublibs

```diff
   1 |- The existance of this file tells the regression reporting programs that the directory contains sub-directories which are libraries.
   1 |\ No newline at end of file
   2 |+ The existence of this file tells the regression reporting programs that the directory contains sub-directories which are libraries.
```

> Username: jsoref  
> Created_at: 2022-11-06 22:05:08 UTC  
> Updated_at: 2022-11-06 22:05:45 UTC  
> Url: https://github.com/boostorg/boost/pull/704#discussion_r1014900125  

Note that I try not to interfere with missing newlines-at-eof, although I personally side w/ git/github an wanting them to be present.


📁 status/explicit-failures-markup.xml

```diff
2248 |     <!-- functional/forward -->
2249 |-     <library name="functional/foward">
2249 |+     <library name="functional/forward">
```

> Username: jsoref  
> Created_at: 2022-11-06 22:05:19 UTC  
> Updated_at: 2022-11-06 22:05:45 UTC  
> Url: https://github.com/boostorg/boost/pull/704#discussion_r1014900138  

?


---

## Comment 2

> Username: mclow  
> Created_at: 2023-01-21 22:12:03 UTC  
> Url: https://github.com/boostorg/boost/pull/704#issuecomment-1399342621  

I have committed these changes (to `develop`) manually, as two commits, to explicitly call out the removal of the line in Jamroot.  Thanks for the patch!

---
