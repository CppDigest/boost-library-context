# #85 Fix #11582, and other enhancements. [Closed]

> Username: BenWiederhake  
> Created at: 2015-08-30 16:22:15 UTC  
> Updated at: 2015-09-07 06:15:57 UTC  
> Closed at: 2015-09-07 06:15:57 UTC  
> Url: https://github.com/boostorg/test/pull/85  

I ran all regression tests, and see no error on my machine.  
  
This pull requests handles several issues. I don't know how you prefer it, so I can split this pull requests into multiple small chunks if you wish.  
  
The first two commits: Fixes #11582 on trac. These commits provide a starting point for discussion. I'm willing to work together on a better solution.  
  
Commits three and four care about typos, I don't expect any problems here. This is a strict superset of the other pull request.  
  
The last commit fixes current issues in <boost/test/detail/throw_exception.hpp>.  
As far as I can see, you are currently working on this and already have fixed in your branch "new_cla"; however you haven't made it available yet. Since this change is atomic and easy to separate, I suggest applying it now instead of waiting on new_cla. YMMV.  
  
EDIT: Github wrongly displays the order of commits in the overview below (huh?)  
In my PR, _first_ add the tests, _then_ provide the fix. Again, YMMV, and I'm willing to change the order if you wish.

---

## Comment 1

> Username: rogeeff  
> Created_at: 2015-09-07 06:15:57 UTC  
> Url: https://github.com/boostorg/test/pull/85#issuecomment-138199979  

BOOST_NORETURN and typo in in develop

---
