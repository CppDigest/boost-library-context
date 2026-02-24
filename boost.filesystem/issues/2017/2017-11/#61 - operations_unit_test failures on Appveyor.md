# #61 - operations_unit_test failures on Appveyor [Closed]

> Username: pdimov  
> Created at: 2017-11-22 02:21:38 UTC  
> Updated at: 2017-11-22 17:06:14 UTC  
> Closed at: 2017-11-22 17:06:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/61  

When the tests are run from the Boost root with `b2 libs/filesystem/test`, the recursive iterator tests in `operations_unit_test` fail on Appveyor for some reason, I'm not sure why; `current_path()` is `C:\projects\boost-root` there. See f.ex. https://ci.appveyor.com/project/pdimov/filesystem/build/1.0.1-develop/job/xg5w0lancn3p8uj7#L809  
  
When I run a similar configuration locally, I don't get this failure, but a recursive iterator from one level up from the Boost root enumerates half of the visible universe, so the test hangs for a very long time.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-11-22 03:13:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/61#issuecomment-346230891  

This is one way to fix it: https://github.com/boostorg/filesystem/commit/1dc51988bbaea447ebf05b2a7d067ee32c55dde9  
  
It's on a branch, but I'll merge it to develop if you like it.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-11-22 03:26:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/61#issuecomment-346232585  

It now occurs to me that these odd failures might have been caused by the `-j 3` option I'm using. Other tests might have been creating and removing files or directories at the same time the directory iteration was performed.  
  
This problem is still present now, although it would probably be very rare. One option is to not run the tests in parallel; the other is to somehow fix them to work even if run in parallel.

---

## Comment 3

> Username: pdimov  
> Created at: 2017-11-22 17:06:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/61#issuecomment-346414070  

Merged to develop; Travis and Appveyor need to be all green to be useful for testing pull requests.
