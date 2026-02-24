# #1085 - Delete merged branches [Open]

> Username: Flamefire  
> Created at: 2025-09-17 08:34:06 UTC  
> Updated at: 2025-09-17 08:34:06 UTC  
> Url: https://github.com/boostorg/boost/issues/1085  

https://github.com/boostorg/boost/branches/all shows currently 24 branches other than develop/master. They should be cleaned up to remove those already merged or no longer required.  
  
It seems most have been merged already or might no longer apply. The stats shown on that overview page are misleading as they show the difference to the default branch, i.e. master but we merge to develop  
  
Using `for i in $(git branch -r | grep -vE 'master|develop'); do echo $i; git rev-list --left-right --count origin/develop..$i; done` to get an overview shows (see bottom) that the following branches can be safely deleted:  
  
- origin/doxygen1.8.15  
- origin/feature/add-histogram  
- origin/feature/add-outcome  
- origin/feature/bcp-namespace  
- origin/feature/boostcpp-py  
- origin/feature/clang-darwin-cxxstd-11  
- origin/feature/docutils  
- origin/feature/fix-status-tools  
- origin/feature/gha  
- origin/feature/travis-cmake  
- origin/feature/travis-cmake-2  
- origin/feature/travis-submodules-j3  
- origin/feature/travis-xenial  
- origin/regression/reporting  
- origin/revert-854-patch-9  
- origin/revert-901-revert-854-patch-9 (shows 1 ahead, but was rebase-merged)  
- origin/feature/remove-travis-packages (rebase-merged or outdated)  
  
It looks like at least some of the others have been merged/rebased/included as well or are outdated.  
  
```  
origin/circle2  
0	11  
origin/doxygen1.8.15  
0	0  
origin/feature/add-histogram  
0	0  
origin/feature/add-outcome  
0	0  
origin/feature/bcp-namespace  
0	0  
origin/feature/boost-install-reqs  
0	1  
origin/feature/boostcpp-py  
0	0  
origin/feature/clang-darwin-cxxstd-11  
0	0  
origin/feature/cmake-config  
0	58  
origin/feature/docutils  
0	0  
origin/feature/fix-status-tools  
0	0  
origin/feature/gha  
0	0  
origin/feature/install-cmake-config  
0	4  
origin/feature/install-cmake-config-2  
0	8  
origin/feature/pkgconfig  
0	2  
origin/feature/remove-travis-packages  
0	4  
origin/feature/reorder-gitmodules  
0	103  
origin/feature/travis-cmake  
0	0  
origin/feature/travis-cmake-2  
0	0  
origin/feature/travis-submodules-j3  
0	0  
origin/feature/travis-xenial  
0	0  
origin/regression/reporting  
0	0  
origin/revert-854-patch-9  
0	0  
origin/revert-901-revert-854-patch-9  
0	1  
```
