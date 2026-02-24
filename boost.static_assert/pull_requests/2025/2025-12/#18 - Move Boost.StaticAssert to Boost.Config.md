# #18 Move Boost.StaticAssert to Boost.Config [Merged]

> Username: Lastique  
> Created at: 2025-12-11 22:50:17 UTC  
> Updated at: 2025-12-26 16:03:29 UTC  
> Merged at: 2025-12-26 15:45:47 UTC  
> Closed at: 2025-12-26 15:45:47 UTC  
> Url: https://github.com/boostorg/static_assert/pull/18  

This follows [this](https://lists.boost.org/archives/list/boost@lists.boost.org/thread/TWKBI5NXF772FGOBZA2FLIC2S7AM6OCQ/#TWKBI5NXF772FGOBZA2FLIC2S7AM6OCQ) discussion on the Boost ML.  
  
Removed library sources, docs, tests and examples; refer to Boost.Config.  
  
This library has been merged into Boost.Config, including sources, documentation, tests and examples. CI configs are removed as there is nothing left to test. index.html updated to redirect to Boost.Config. README.md and meta/libraries.json were updated to mention that the library was merged into Boost.Config.  
  
CMakeLists.txt and build.jam are retained to define Boost.StaticAssert targets, which are now empty and only add the dependency on Boost.Config. This is to avoid breaking downstream libraries that have Boost.StaticAssert as a dependency. These users will need to be updated, and at that point this entire git submodule can be deleted.  
  
This PR must be merged together with https://github.com/boostorg/config/pull/531.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-12-11 23:58:43 UTC  
> Url: https://github.com/boostorg/static_assert/pull/18#issuecomment-3644303039  

Let's not delete the tests from here. They must still pass.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-12-12 01:05:01 UTC  
> Url: https://github.com/boostorg/static_assert/pull/18#issuecomment-3644460030  

I moved the tests to Boost.Config, do we need to leave the duplicates here? And the CI on this PR will fail anyway because the header is removed and not yet added to Boost.Config's develop.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-12-12 09:25:32 UTC  
> Url: https://github.com/boostorg/static_assert/pull/18#issuecomment-3645651110  

Yes, I prefer to have the tests remain here.

---

## Comment 4

> Username: Lastique  
> Created_at: 2025-12-12 12:02:18 UTC  
> Url: https://github.com/boostorg/static_assert/pull/18#issuecomment-3646205017  

Ok, I have restored tests and examples (which are built as part of testing).

---
