# #525 Add compile-only tests verifying headers are self-contained [Merged]

> Username: mloskot  
> Created at: 2018-10-28 19:55:53 UTC  
> Updated at: 2018-11-21 13:18:55 UTC  
> Merged at: 2018-11-15 02:04:10 UTC  
> Closed at: 2018-11-15 02:04:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/525  

Add Boost.Jam rule generating test target for each header found in specified location.  
List test suites (target aliases) for groups of headers.  
Build self_contained_headers test only if `CI` environment variable is NOT defined or on-demand, if `TEST_HEADERS` environment variable is defined, or if `b2 test/self_contained_headers` is requested from command line.  
  
Related to issue #523  
  
-----  
  
First, this PR does not attempt to fix any headers, not yet.  
  
I've run all the headers tests on 24 CPUs (48 logical cores) as follows:  
  
```shell  
cd libs/geometry  
b2 -j 16 test/self_contained_headers >build.log 2>&1  
```  
  
AFAICT, it compile-tests *~900* headers in less than *4 minutes*:  
  
```shell  
cat build.log | grep -E -o "c\+\+.+\~hpp\.test" | sort | uniq | wc -l  
898  
cat build.log | grep -E -o "passed.+~hpp\.test" | wc -l  
547  
cat build.log | grep -E -o "failed.+~hpp\.test" | wc -l  
350  
```  
  
And, I have only included several extensions, see `alias`-es  and comments in `test/self_contained_headers/Jamfile`.  
  
-----  
  
To run the tests on CI services, it should be enough to define environment variable `TEST_HEADERS=1`  for one just build job. For example, Boost.GIL defines it for first job only https://travis-ci.org/boostorg/gil/jobs/447353675

---

## Comment 1

> Username: vissarion  
> Created_at: 2018-10-30 08:17:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/525#issuecomment-434209790  

Thanks, this is useful.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-10-30 09:28:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/525#issuecomment-434229621  

Thanks!

---

## Comment 3

> Username: awulkiew  
> Created_at: 2018-11-14 13:42:40 UTC  
> Updated_at: 2018-11-14 13:45:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/525#issuecomment-438665620  

Wow, thanks! Just to be sure, does it work with CircleCI? (EDIT: And by "it" I mean bjam properly defining `CI` env ;)).

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-11-14 14:19:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/525#issuecomment-438677386  

I see no reason why it should not work on CircleCI - I mean, those tests should be automatically filtered there as on Travis or AppVeyor.  
  
CircleCI does define `CI` as [built-in environment variable](https://circleci.com/docs/2.0/env-vars/#built-in-environment-variables) and `b2` just reads the environment variables (no `CI` needs to be defined in any `Jamfile`).

---

## Comment 5

> Username: awulkiew  
> Created_at: 2018-11-15 02:02:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/525#issuecomment-438888485  

Ok, thanks for the explanation.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2018-11-21 02:38:10 UTC  
> Updated_at: 2018-11-21 02:41:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/525#issuecomment-440507401  

@mloskot This change polluted our regression matrix (https://www.boost.org/development/tests/develop/developer/geometry.html). I'm considering disabling it by default. Or maybe do you have some other ideas?  
  
EDIT: Furthermore on windows the generated paths are too long even with `--abbreviate-paths`

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-11-21 05:10:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/525#issuecomment-440533083  

@awulkiew I'm trying to make those tests on-demand only in #529  
  
I'm not sure how to avoid the long paths. Since the `.cpp` file is generated, I assumed that is not an issue, so ignored it.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2018-11-21 13:18:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/525#issuecomment-440659123  

If they are not run by default it is no longer an issue since we can just run them on linux. Thanks!

---
