# #676 Fix for issue 675 [Merged]

> Username: mborland  
> Created at: 2021-08-22 16:55:28 UTC  
> Updated at: 2021-08-23 20:22:49 UTC  
> Merged at: 2021-08-23 14:48:28 UTC  
> Closed at: 2021-08-23 14:48:29 UTC  
> Url: https://github.com/boostorg/math/pull/676  

Fixes for #675. Allows for switching of `BOOST_MATH_HEADER_DEPRECATED` based on standalone context. Fixes behavior of standalone implementation.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-08-23 04:17:49 UTC  
> Url: https://github.com/boostorg/math/pull/676#issuecomment-903430068  

Ready to go?

---

## Comment 2

> Username: mborland  
> Created_at: 2021-08-23 14:35:57 UTC  
> Url: https://github.com/boostorg/math/pull/676#issuecomment-903832023  

> Ready to go?  
  
Yes; this is good.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-08-23 17:14:55 UTC  
> Url: https://github.com/boostorg/math/pull/676#issuecomment-903960555  

Merging.  Do we need to run the new test in standalone mode as well?

---

## Comment 4

> Username: thesamesam  
> Created_at: 2021-08-23 17:18:57 UTC  
> Url: https://github.com/boostorg/math/pull/676#issuecomment-903963394  

Thanks both. This indeed resolves the issue downstream for us in Gentoo. Cheers for the quick fix!

---

## Comment 5

> Username: NAThompson  
> Created_at: 2021-08-23 17:52:05 UTC  
> Url: https://github.com/boostorg/math/pull/676#issuecomment-903984406  

@jzmaddock : I want to do a new standalone release upon resolution of #677 ; do we have a good CI solution for testing our standalone releases?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-08-23 18:30:27 UTC  
> Url: https://github.com/boostorg/math/pull/676#issuecomment-904011534  

We would need to merge to master, and then just check that the CI run on master is clean.

---

## Comment 7

> Username: mborland  
> Created_at: 2021-08-23 20:18:24 UTC  
> Url: https://github.com/boostorg/math/pull/676#issuecomment-904091930  

> @jzmaddock : I want to do a new standalone release upon resolution of #677 ; do we have a good CI solution for testing our standalone releases?  
  
None of the current tests in the CI battery define `BOOST_MATH_STANDALONE`. I can open a PR for a new YAML file that runs against a different branch say `standalone_master`. That way you could just run it periodically instead of every time something is committed to develop. Currently the only thing that is tested in standalone mode is the compile tests using CMake. Thoughts?

---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-08-23 20:22:49 UTC  
> Url: https://github.com/boostorg/math/pull/676#issuecomment-904095177  

Our CI suite is already pretty expensive. Maybe one configuration on each merge request, and then a big run on `master` or `standalone_master`.   
  
We should probably see how @jzmaddock feels about having the standalone a bit out of sync with the Boost release cadence.

---
