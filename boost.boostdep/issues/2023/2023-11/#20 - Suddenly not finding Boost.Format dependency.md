# #20 - Suddenly not finding Boost.Format dependency? [Closed]

> Username: jzmaddock  
> Created at: 2023-11-06 16:43:14 UTC  
> Updated at: 2023-11-06 19:22:55 UTC  
> Closed at: 2023-11-06 19:22:55 UTC  
> Url: https://github.com/boostorg/boostdep/issues/20  

Starting some time in the last 24 hours many of the Boost.Math CI tests are failing due to a missing boost/format.hpp, see for example: https://github.com/boostorg/math/actions/runs/6768902679/job/18401577644?pr=1046  
  
The annoying thing is I see no changes in Math, Boostdep, or Format that would cause this!  
  
Hoping you have more ideas than I do...  
  
And yes, this is a bit of a release blocker for us :(  
  
Refs https://github.com/boostorg/math/pull/1046.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-11-06 16:57:04 UTC  
> Updated at: 2023-11-06 16:59:11 UTC  
> Url: https://github.com/boostorg/boostdep/issues/20#issuecomment-1795490168  

You need to tell `depinst` to look into `tools` for dependencies because  
  
https://github.com/boostorg/math/blob/11e2348372088f0ede36ff6cbf2d70c8e1783a3b/test/Jamfile.v2#L1678  
  
`boost/format.hpp` is only included from sources in `tools`.  
  
The reason this worked before is that Boost.Format was a (false) dependency of PropertyTree, and I removed it.  
  
https://github.com/boostorg/property_tree/commit/7ab781ee390d294ab37cdcc063bedd0656c0fa82  
  
While there, you should also fix it to look in `example` as well, for the same reason. Do that by adding `-I example -I tools` to the `depinst` invocation here:  
  
https://github.com/boostorg/math/blob/11e2348372088f0ede36ff6cbf2d70c8e1783a3b/.github/workflows/ci.yml#L61
