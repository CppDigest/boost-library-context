# #268 Add test/Jamfile [Closed]

> Username: pdimov  
> Created at: 2017-11-28 21:34:13 UTC  
> Updated at: 2021-10-02 21:00:06 UTC  
> Closed at: 2018-01-09 15:00:49 UTC  
> Url: https://github.com/boostorg/build/pull/268  

This adds a `Jamfile` in `test` that just calls `python test_all.py`, so that the `b2 submodule-dir/test` command invoked by the superproject Travis runs the tests.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-01-09 14:45:10 UTC  
> Url: https://github.com/boostorg/build/pull/268#issuecomment-356303692  

This doesn't work for a few reasons:  
- ``using python ;`` in user-config.jam will break it because of duplicate registration of the PY type.  
- ``b2 path/to/boost-build/test`` fails because the tests assume that the current working directory is test/ in several places.  
- There are no explicit dependencies and no include scanner is set up, so the tests will only be rebuilt when test_all.py is modified.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2018-01-09 15:00:49 UTC  
> Url: https://github.com/boostorg/build/pull/268#issuecomment-356308511  

I used run-pyd instead of run.  This requires that python can be fully configured (including finding libraries and headers), but that's more a problem with the way python.jam is set up.  In the long run, I think letting python.jam handle running python scripts is the right way to go.  We have the facilities to do more fine-grained configuration checks, so python.jam should be adjusted to allow only finding the python executable at some point.

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:00:05 UTC  
> Url: https://github.com/boostorg/build/pull/268#issuecomment-932819744  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
