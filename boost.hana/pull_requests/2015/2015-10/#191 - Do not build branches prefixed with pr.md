# #191 [Travis] Do not build branches prefixed with pr/ [Merged]

> Username: ldionne  
> Created at: 2015-10-20 23:47:06 UTC  
> Updated at: 2016-06-11 19:48:57 UTC  
> Merged at: 2015-10-21 00:11:15 UTC  
> Closed at: 2015-10-21 00:11:15 UTC  
> Url: https://github.com/boostorg/hana/pull/191  

This avoids building both the push commit and the pull request, which is frankly annoying. Note that this only concerns pull requests coming from branches _inside_ the repository, since push commits from forks are not normally built on Travis.

---
