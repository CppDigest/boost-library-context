# #76 Add basic CI [Merged]

> Username: Flamefire  
> Created at: 2022-05-17 13:50:05 UTC  
> Updated at: 2022-05-27 13:16:08 UTC  
> Merged at: 2022-05-27 13:16:04 UTC  
> Closed at: 2022-05-27 13:16:04 UTC  
> Url: https://github.com/boostorg/locale/pull/76  

Add CI tests on Windows, Linux & OSX via GHA and Appveyor based on Boost.CI  
  
This is useful as a judgement for contributions (there are quite a few open PRs and issues) to make sure they at least don't break the build.  
  
For integration with Boost.CMake a "regular" CMake test as well as a consumer CMake test was added (i.e. test that linking against the installed library works)  
  
Note: Due to various issues, this PR is expected to fail CI. I'll fix the various issues in follow-ups.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-05-17 14:48:21 UTC  
> Url: https://github.com/boostorg/locale/pull/76#issuecomment-1128966460  

@artyom-beilis If you don't mind I'll go ahead and start creating and merging CI PRs. I basically took a branch I've been working on to get at least as much CI on as I could and divided that into more or less independent branches for which I'll create a PR each (see the 3 I've started with) so one can follow what was done and I can document the reasoning for each of them in those PRs (as an extension to the commit messages)  
  
The final result (i.e. my work branch) is https://github.com/Flamefire/locale/tree/feature/ci and what will come up can be seen at https://github.com/Flamefire/locale/branches/active  
  
I'll also do mostly "Rebase-merges", i.e. there won't be a merge commit for every PR but the new commits will be added on top of develop as most PRs will likely only be 1-2 commits anyway and I don't like the clutter in the history.  
  
Any objections?

---
