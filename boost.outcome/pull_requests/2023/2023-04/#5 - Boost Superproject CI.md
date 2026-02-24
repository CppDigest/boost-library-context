# #5 Boost Superproject CI [Closed]

> Username: alandefreitas  
> Created at: 2023-04-05 01:31:48 UTC  
> Updated at: 2023-04-26 19:48:28 UTC  
> Closed at: 2023-04-26 19:48:28 UTC  
> Url: https://github.com/boostorg/outcome/pull/5  

This PR includes CI to always check if the boostified library also works with the boost superproject accordingly. It seems appropriate to open this PR here since most changes are not related to the standalone library.   
  
Anything can be adapted to your conventions and style. The focus of PR has been on just making it work.  
  
So here we go:  
  
- `ci.yml` includes a workflow that tests the library like `boostorg/boost` does. If you look at the [history](https://github.com/alandefreitas/outcome/actions) of this action, you'll see the problem with `hooks.cpp` is not the only problem we have when we use the `boostorg/boost` workflow. It was just the first error that was breaking the super-project, so removing it revealed other errors.   
- `.github/actions/*.yml` are just 2 helper actions to make it easier to clone the subset of boost you need efficiently. You can adapt the whole CI thing to your style or just do that later. But we *must* have something in place to check if the boostified library is working to avoid breaking the super-project build.  
- `CMakeLists.txt`: I included an initial script that allows your library to be built as the project root. A lot of boost libraries use this pattern as an extension to the automatically generated script. Not only to provide a way for users to use it independently but especially for developers to have an easier workflow to work on the library and to simplify things in CI, because you just give it the boost src dir instead of having to reconstruct a boost distribution patched with your library. You can adapt that the way you want but removing it might make the CI scripts significantly more complex.  
- `hooks.cpp`: I found it interesting that you're using the `windows-2019` container in the standalone tests. I have no idea why you didn't have the same problem there. It's either something the standalone version has or something in your `.ci.cmake`. Nonetheless, it was broken in any version of VS 2019 I could put my hands on: locally and in CI. There's a lot of meta-programming in those constructors which takes time to break down and "debug" (if static_asserts count as debugging). In most of the cases, constructors led to a `std::is_convertible` test that passes in MSVC 2019 but not in other compilers. `std::is_convertible` in VS 2019 has lots of false positives that make the constructors ambiguous. There's no single `basic_result` constructor we can fix to just make this work. Considering there are 29 constructors in `basic_result`, you have a major design decision to make. You can either come up with another `is_implicitly_constructible` trait that works for all compilers with the intended behavior you have in mind, change what features are available in VS 2019, or stop supporting VS 2019. Maybe you'll find something in `boost::system::result` that could help you make that choice. In this PR, I explicitly deleted some `hook_test::error_code` constructors so MSVC understands they are deleted. This should be OK since the change is beyond the scope of the test. Fixing one error led to another until I needed a `BOOST_WORKAROUND` to make the very last test pass. There's no easy solution to this without rethinking the requirements of these constructors.  
- `coroutine_support.ipp`: the condition for detecting coroutine support had a false positive for some versions of clang. I conditionally used `BOOST_NO_CXX20_HDR_COROUTINE` in a way that works but should also not affect the standalone library, since the macro won't be defined and it will pass through to the previous conditions. This only improves Boost.Outcome. For a more consistent solution for the standalone library, you would have to have a look at the conditions `boost/config.hpp` uses to detect this coroutine support.  
- `expected-pass.cpp` had an error quite similar to `hooks.cpp`. I also included a workaround there to avoid breaking the superproject. These workarounds should translate well to the standalone library, although they don't fix the problem there, as the symbols don't exist anyway.  
- `comparison.cpp`: that was [an error in GCC10](https://github.com/alandefreitas/outcome/actions/runs/4612458196/jobs/8153374639#step:5:445). Template instantiation depth exceeded the maximum even after I raised it to 5000. There must be some recursive instantiation there. I included another workaround to avoid breaking the super project as the solution to this will require some more heavy meta-programming testing.    
  
As I said, there's a lot in here and anything can be adapted to your conventions and style. I just focused on making it work at first so we can now adapt this however it works best for you. The only important thing is that we keep continuously testing whatever is converted from the standalone library.

---

## Comment 1

> Username: ned14  
> Created_at: 2023-04-05 13:46:58 UTC  
> Url: https://github.com/boostorg/outcome/pull/5#issuecomment-1497516781  

Thanks for all the work here.  
  
So tl;dr; here is just disable CI runs on VS2019, and change the docs to mention VS2019 is flaky?  
  
The GCC 10 issue is a bug in their compiler which should be fixed in a later point release.  
  
The boostorg/outcome repo is wholly auto generated from the ned14/outcome repo, so no PR can be merged here, it'll just get deleted. I'll try to find the time to cherry pick over your changes after the next Boost release goes out.  
  
Thanks for everything here, lots of value added.

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-04-05 16:23:32 UTC  
> Url: https://github.com/boostorg/outcome/pull/5#issuecomment-1497778772  

Thanks. Sure.  
  
> So tl;dr; here is just disable CI runs on VS2019, and change the docs to mention VS2019 is flaky?  
  
Sure. That's an option. I don't know what a formal replacement for "flaky" would be tough.  
  
However, I would ensure CI matches what the docs say. That is:  
  
- Docs say you support VC2019 but it has these problems. CI disables only the specific flaky tests.  
- Docs say you do not support VC2019. CI disables all runs on VS2019.  
  
In any of these options, I would also include a condition in CMakeLists.txt to avoid compiling the flaky tests (in the first option) or any tests (second option) if the compiler is VS <=2019 to avoid breaking the build. This not only allows Peter to remove `BOOST_EXCLUDE_LIBRARIES=outcome` from the super project CI but also helps users need to be able to run `cmake --build` on the whole thing without excluding libraries.  
  
> The GCC 10 issue is a bug in their compiler which should be fixed in a later point release.  
  
Yes. There are lots of bugs in these old compilers. Kind of the same options as above (exclude-all or exclude-flaky). We have to make a decision and keep testing it though.  
  
> The boostorg/outcome repo is wholly auto generated from the ned14/outcome repo, so no PR can be merged here, it'll just get deleted. I'll try to find the time to cherry pick over your changes after the next Boost release goes out.  
  
Yes. I noticed that. 90% of stuff here is related to boost only. So I thought boostorg/outcome was a good place for this to go. In the standalone project, it would probably go to some template you use to boostify it. I understand how personal how things are and you have to adjust them to your style. I just recommend you move everything to the template, especially the CI superproject tests.

---

## Comment 3

> Username: ned14  
> Created_at: 2023-04-26 19:48:28 UTC  
> Url: https://github.com/boostorg/outcome/pull/5#issuecomment-1523956888  

I believe all issues found during your work are now resolved:  
  
https://github.com/boostorg/outcome/actions/runs/4812666803/jobs/8568198656  
  
Thanks for the time investigating these issues, and for the suggested fixes. I have very little free time right now, your help was very useful.

---
