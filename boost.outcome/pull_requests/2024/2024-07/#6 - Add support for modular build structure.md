# #6 Add support for modular build structure. [Closed]

> Username: grafikrobot  
> Created at: 2024-07-20 22:34:07 UTC  
> Updated at: 2024-07-25 22:31:42 UTC  
> Closed at: 2024-07-25 22:31:41 UTC  
> Url: https://github.com/boostorg/outcome/pull/6  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
- https://github.com/boostorg/auto_index/pull/7  
- https://github.com/boostorg/bcp/pull/20  
- https://github.com/boostorg/boost_install/pull/69  
- https://github.com/boostorg/boost/pull/890  
- https://github.com/boostorg/boostbook/pull/25  
- https://github.com/boostorg/boostdep/pull/21  
- https://github.com/boostorg/docca/pull/143  
- https://github.com/boostorg/inspect/pull/19  
- https://github.com/boostorg/quickbook/pull/25  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: ned14  
> Created_at: 2024-07-22 10:33:14 UTC  
> Url: https://github.com/boostorg/outcome/pull/6#issuecomment-2242633371  

The PR is not clean.  
  
Also any changes applied to this repo will get wiped next automatic rebuild.  
  
You should either open the PR against standalone Outcome or let me port the change delta.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-07-22 12:50:09 UTC  
> Url: https://github.com/boostorg/outcome/pull/6#issuecomment-2242880319  

> The PR is not clean.  
  
There where changes after I composed my fork. That will get resolved next time I merge into the PR branch.  
  
> Also any changes applied to this repo will get wiped next automatic rebuild.  
>   
> You should either open the PR against standalone Outcome or let me port the change delta.  
  
Given that I can't test the build system in that standalone branch you will have to port the changes to the standalone version. In Predef, where I also have a standalone version, I made the conversion scripts bidirectional. Something to consider for the future?

---
