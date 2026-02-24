# #13 Take input filename on the command line to test/image. [Closed]

> Username: NiklasAngare  
> Created at: 2015-03-21 15:39:31 UTC  
> Updated at: 2018-04-06 17:39:06 UTC  
> Closed at: 2018-04-06 16:05:29 UTC  
> Url: https://github.com/boostorg/gil/pull/13  

This enables test runners to copy the file to the target. It also makes the code that looks for the file in a different path unnecessary since Boost.Build will pass the proper relative path.  
  
This should fix failures for test runners NA-QNX650-SP1-ARM and CrystaX.NET\* on master. Note that this fix is irrelevant for develop as the test no longer uses an input file there.  
  
It may be more appropriate to merge develop to master since that hasn't been done in over four years.

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2018-03-26 16:56:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/13#pullrequestreview-106988074  

LGTM. I only suggested a few minor changes

📁 test/image.cpp

```diff
 572 |+     {
 573 |+         std::cerr << "Checksum file name argument missing" << std::endl;
 574 |+         return 1;
```

> Username: mloskot  
> Created_at: 2018-03-26 16:55:25 UTC  
> Updated_at: 2018-03-26 16:56:27 UTC  
> Url: https://github.com/boostorg/gil/pull/13#discussion_r177162326  

`return EXIT_FAILURE` please

---

📁 test/image.cpp

```diff
 582 |-         else {
 583 |-             std::cerr << "Unable to open gil_reference_checksums.txt"<<std::endl;
 584 |-             return 1;
```

> Username: mloskot  
> Created_at: 2018-03-26 16:55:45 UTC  
> Updated_at: 2018-03-26 16:56:27 UTC  
> Url: https://github.com/boostorg/gil/pull/13#discussion_r177162435  

`return EXIT_FAILURE`

---

📁 test/image.cpp

```diff
 585 |-         }
 580 |+         std::cerr << "Unable to open " << argv[1] << std::endl;
 581 |+         return 1;
```

> Username: mloskot  
> Created_at: 2018-03-26 16:55:50 UTC  
> Updated_at: 2018-03-26 16:56:27 UTC  
> Url: https://github.com/boostorg/gil/pull/13#discussion_r177162470  

`return EXIT_FAILURE`

---

📁 test/image.cpp

```diff
 582 |     }
 583 | 
 584 |     return 0;
```

> Username: mloskot  
> Created_at: 2018-03-26 16:56:04 UTC  
> Updated_at: 2018-03-26 16:56:27 UTC  
> Url: https://github.com/boostorg/gil/pull/13#discussion_r177162543  

`return EXIT_SUCCESS`


---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-03-26 20:00:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/13#pullrequestreview-107051233  

I very much like the idea. (I couldn't figure out how to do this with Boost.Build, and so I had to introduce a dependency on Boost.FileSystem on the `develop` branch to compute the (relative) location of the checksum file.)  
  
Please note that this PR is against `master`, though. It needs to be reworked to be applied to `develop`, which has many more tests that need similar adjustments.  
The good news will be that the Boost.GIL tests no longer depend on Boost.FileSystem (and Boost.System).

---

## Review 3 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2018-03-26 20:06:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/13#pullrequestreview-107053042  

(please re-apply against the `develop` branch. `master` will only ever get merges from `develop` once the latter is in a stable state, and no pending releases block merges.)

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-03-26 20:16:06 UTC  
> Updated_at: 2018-03-26 20:16:13 UTC  
> Url: https://github.com/boostorg/gil/pull/13#issuecomment-376297192  

Ups, I missed the base branch issue. Yes I second @stefanseefeld request to apply against develop.  
  
I'm not sure if an existing PR can be re-based on GitHub. If it can not, it may be necessary to close this PR and re-submit @NiklasAngare -s work in new PR based on develop.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-04-06 12:04:35 UTC  
> Updated_at: 2018-04-06 12:05:10 UTC  
> Url: https://github.com/boostorg/gil/pull/13#issuecomment-379233238  

This has been partially implemented, only `image.cpp` part, in SHA-1: 8e5501ecf06b47b59a3cf8a901d746ac2c3aed97  
  
Currently, default location of `gil_reference_checksums.txt` is still resolved from `__FILE__`. Note, especially on Windows, `-FC` compiler switch may be preferred to get the absolute path from `__FILE__`.  
  
I guess, the `Jamfile` change passing the `gil_reference_checksums.txt` file from command line could be copied too. Shall we copy it and then close this PR?

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2018-04-06 12:20:48 UTC  
> Url: https://github.com/boostorg/gil/pull/13#issuecomment-379236546  

I'm very reluctant with platform-specific solutions such as the `-FC` compiler switch.  
I think ultimately we should change all tests that require a checksum file to take that as command-line argument (with the associated changes to the respective `Jamfile`s.  
Anything that gets us closer to that goal is fine.

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-04-06 12:26:08 UTC  
> Url: https://github.com/boostorg/gil/pull/13#issuecomment-379237652  

OK  
  
> I'm very reluctant with platform-specific solutions such as the -FC compiler switch.  
  
That was just a non-intrusive workaround while developing CMake setup.

---

## Comment 8

> Username: mloskot  
> Created_at: 2018-04-06 16:06:13 UTC  
> Updated_at: 2018-04-06 17:39:06 UTC  
> Url: https://github.com/boostorg/gil/pull/13#issuecomment-379300179  

@NiklasAngare 's change has been cherry-picked with adjustments following recent changes in `develop`.  
  
Thanks again for the fix.

---
