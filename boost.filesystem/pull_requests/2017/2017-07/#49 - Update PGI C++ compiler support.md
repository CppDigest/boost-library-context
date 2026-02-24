# #49 Update PGI C++ compiler support [Merged]

> Username: dkolsen-pgi  
> Created at: 2017-07-26 19:37:23 UTC  
> Updated at: 2018-09-06 02:15:43 UTC  
> Merged at: 2018-09-06 02:15:43 UTC  
> Closed at: 2018-09-06 02:15:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/49  

Remove an #if from operations.cpp that used to be necessary for the file to compile with PGI C++, but causes compilation errors with PGI C++ compilers of the last few years.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-07-26 21:44:51 UTC  
> Url: https://github.com/boostorg/filesystem/pull/49#issuecomment-318191894  

If the workaround is still needed for older versions, why not guard it with a version check?

---

## Comment 2

> Username: dkolsen-pgi  
> Created_at: 2017-07-26 21:52:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/49#issuecomment-318193679  

There is no version check because we don't know of any customers who are still using a PGI compiler old enough to require that workaround.  If there are any such users, there's a good chance that they also have an older version of Boost that won't have this change.  In my opinion, the potential benefit of a version check is smaller than the cost of cluttering up the code.

---

## Comment 3

> Username: alexeikh  
> Created_at: 2018-05-11 09:11:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/49#issuecomment-388308218  

(I am not Boost project member, nor have commit rights to Boost, just trying to contribute with the code review.)  
  
I think, this pull request is nice and @dkolsen-pgi 's reasoning make sense. Old workarounds clutter the code and are liabilities. They should have finite lifetime. This workaround was committed in 2011 (commit d776d3985fcf76572375bb0a885f250d014b6ea1). Time to retire it, IMO.  
  
The pull request comes from an employee of the company which produces the PGI compiler, IIUC. Which gives the PR more credibility.  
  
I am voting, for what it's worth, for this PR to be merged in.

---
