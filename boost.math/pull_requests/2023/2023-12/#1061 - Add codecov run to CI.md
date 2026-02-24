# #1061 Add codecov run to CI [Merged]

> Username: mborland  
> Created at: 2023-12-22 12:41:06 UTC  
> Updated at: 2024-01-05 08:00:29 UTC  
> Merged at: 2024-01-05 08:00:25 UTC  
> Closed at: 2024-01-05 08:00:25 UTC  
> Url: https://github.com/boostorg/math/pull/1061  



---

## Comment 1

> Username: mborland  
> Created_at: 2023-12-22 14:27:49 UTC  
> Url: https://github.com/boostorg/math/pull/1061#issuecomment-1867749395  

@Flamefire This is the same codecov yaml from boost-ci I have put in a few libraries now. Any idea why the setup boost step is failing with missing library? It looks like an error in `get_libname`? https://github.com/boostorg/math/actions/runs/7299976550/job/19894765017#step:10:126. Thanks for the assistance.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-12-22 16:54:05 UTC  
> Url: https://github.com/boostorg/math/pull/1061#issuecomment-1867893077  

Looks like our libraries.json was not formatted as expected. Everything is working now.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2023-12-23 10:25:01 UTC  
> Url: https://github.com/boostorg/math/pull/1061#issuecomment-1868263044  

> Looks like our libraries.json was not formatted as expected. Everything is working now.  
  
To be specific: You put your description into a list which seems to be valid and used by libraries containing sub-libraries. I don't see a reason to use this for single-library libraries where you can just use a dict (as you did in your change)     
I'm not sure how that JSON file is defined but seemingly that list has to be supported by Boost.CI -> https://github.com/boostorg/boost-ci/pull/225  
  
Currently your PR fails due to a timeout on GHA. You can possibly split it into multiple jobs instead of building release/debug and static/shared in one job to reduce the time. https://github.com/boostorg/math/pull/1061/files#diff-512f30627f95d7fcdc76dc362d896351e9a4539994b7ab00b41979e653a6d0a9R170 should give you an idea how. Otherwise feel free to ask.     
I guess it would even be enough to build just the static OR the shared variant for coverage which already cuts down the time in half. But you can also have one job each, codecov combines all uploads for a commit.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-12-23 11:43:26 UTC  
> Url: https://github.com/boostorg/math/pull/1061#issuecomment-1868276352  

> > Looks like our libraries.json was not formatted as expected. Everything is working now.  
>   
> To be specific: You put your description into a list which seems to be valid and used by libraries containing sub-libraries. I don't see a reason to use this for single-library libraries where you can just use a dict (as you did in your change) I'm not sure how that JSON file is defined but seemingly that list has to be supported by Boost.CI -> [boostorg/boost-ci#225](https://github.com/boostorg/boost-ci/pull/225)  
  
A release or two ago we went from having sub-libraries listed in the meta to just overall Boost.Math so that makes sense.  
  
>   
> Currently your PR fails due to a timeout on GHA. You can possibly split it into multiple jobs instead of building release/debug and static/shared in one job to reduce the time. https://github.com/boostorg/math/pull/1061/files#diff-512f30627f95d7fcdc76dc362d896351e9a4539994b7ab00b41979e653a6d0a9R170 should give you an idea how. Otherwise feel free to ask. I guess it would even be enough to build just the static OR the shared variant for coverage which already cuts down the time in half. But you can also have one job each, codecov combines all uploads for a commit.  
  
Thanks for the tips.

---

## Comment 5

> Username: mborland  
> Created_at: 2023-12-23 21:34:08 UTC  
> Url: https://github.com/boostorg/math/pull/1061#issuecomment-1868373687  

@Flamefire so my tests are finishing in about an hour running the full test suite, but the upload step is timing out at 5 hours. In our normal CI we cut it in half github_ci_block_1 and 2. Is there a way to run these two test suites individually instead of together? Hopefully that'll reduce the upload time by half.

---

## Review 6 [Commented]

> Username: Flamefire  
> Created_at: 2023-12-24 08:04:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1061#pullrequestreview-1795532768  

> @Flamefire so my tests are finishing in about an hour running the full test suite, but the upload step is timing out at 5 hours. In our normal CI we cut it in half github_ci_block_1 and 2. Is there a way to run these two test suites individually instead of together? Hopefully that'll reduce the upload time by half.  
  
5 hours... Never thought that collecting the coverage data could take so long.  
  
Yes splitting the 2 suites might help, although it seems to be the amount of files included which is the issue, but let's try.  
  
https://github.com/boostorg/math/pull/1061/commits/d1a5ae0902b5f4459b314f6a0e2b6565d3aab7aa was very close already. It just forgot that b2 runs in the boost root which enables some more "magic"/avoids known issues.  
  
Check if the suggested changes work

📁 .github/workflows/codecov.yml

```diff
 173 |+           # Set the (B2) target(s) to build, defaults to the test folder of the current library
 174 |+           # Can alternatively be done like this in the build step or in the build command of the build step, e.g. `run: B2_TARGETS=libs/$SELF/doc ci/build.sh`
 175 |+           B2_TARGETS: ${{ matrix.suite }}
```

> Username: Flamefire  
> Created_at: 2023-12-24 07:56:23 UTC  
> Updated_at: 2023-12-24 08:04:22 UTC  
> Url: https://github.com/boostorg/math/pull/1061#discussion_r1435780251  

```diff  
-          B2_TARGETS: ${{ matrix.suite }}  
+          B2_TARGETS: libs/math/test//${{ matrix.suite }}  
```

---

📁 .github/workflows/codecov.yml

```diff
 183 |+         if: '!matrix.coverity'
 184 |+         run: ../../../b2 toolset=${{ matrix.compiler }} cxxstd=${{ matrix.cxxstd }} ${{ matrix.suite }} define=CI_SUPPRESS_KNOWN_ISSUES define=SLOW_COMPILER cxxflags=-fkeep-static-functions --coverage' linkflags=--coverage address-model=64 link=shared variant=debug -j5
 185 |+         working-directory: ../boost-root/libs/math/test
```

> Username: Flamefire  
> Created_at: 2023-12-24 08:00:55 UTC  
> Updated_at: 2023-12-24 08:04:22 UTC  
> Url: https://github.com/boostorg/math/pull/1061#discussion_r1435780963  

```diff  
-        working-directory: ../boost-root/libs/math/test  
+        run: ci/build.sh  
```

---

📁 .github/workflows/codecov.yml

```diff
 168 |+           B2_STDLIB: ${{matrix.stdlib}}
 169 |+           # More entries can be added in the same way, see the B2_ARGS assignment in ci/enforce.sh for the possible keys.
 170 |+           # B2_DEFINES: ${{matrix.defines}}
```

> Username: Flamefire  
> Created_at: 2023-12-24 08:03:56 UTC  
> Updated_at: 2023-12-24 08:04:22 UTC  
> Url: https://github.com/boostorg/math/pull/1061#discussion_r1435781369  

```diff  
-          # B2_DEFINES: ${{matrix.defines}}  
+          B2_DEFINES: CI_SUPPRESS_KNOWN_ISSUES=1 SLOW_COMPILER=1  
```


---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2023-12-28 14:41:43 UTC  
> Url: https://github.com/boostorg/math/pull/1061#issuecomment-1871236891  

## Welcome to [Codecov](https://codecov.io?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) :tada:  
  
Once merged to your default branch, Codecov will compare your coverage reports and display the results in this comment.  
  
Thanks for integrating Codecov - We've got you covered :open_umbrella:

---
