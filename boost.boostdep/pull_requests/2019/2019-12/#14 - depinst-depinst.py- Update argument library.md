# #14 depinst/depinst.py: Update argument library [Merged]

> Username: e-kwsm  
> Created at: 2019-12-15 01:40:57 UTC  
> Updated at: 2019-12-15 23:58:55 UTC  
> Merged at: 2019-12-15 18:13:24 UTC  
> Closed at: 2019-12-15 18:13:24 UTC  
> Url: https://github.com/boostorg/boostdep/pull/14  

At present `depinst.py` does not update argument itself, so, for example,  
```shellsession  
$ git clone --depth 1 https://github.com/boostorg/boost.git  
$ cd boost  
$ git submodule update --init tools/boostdep  
$ python3 tools/boostdep/depinst/depinst.py -v -g'--depth=1' format  
...  
Submodule path 'tools/build': checked out 'ceddb211c6e0ab7f0abf6c253f1f6e392d6a38b4'  
Directories to scan: include src test  
Scanning module format  
Scanning directory libs/format/include  
Scanning directory libs/format/src  
Scanning directory libs/format/test  
$ git submodule | fgrep format  
-894f465d843d999b5c024a49f90a5a1013edd368 libs/format  
```  
`format` and its dependence (e.g., `optional`) are not updated because `libs/format` is empty.  
  
The PR fixes this.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-12-15 02:06:21 UTC  
> Url: https://github.com/boostorg/boostdep/pull/14#issuecomment-565770314  

This is by design. The module is supposed to be already updated or present before invoking depinst. One (maybe the primary) purpose of depinst is to be used in Boost library CI, such as Travis and Appveyor, and in this case, we already have the library checked out, and we don't want the one from the superproject as it may be older or from a different branch.

---

## Comment 2

> Username: e-kwsm  
> Created_at: 2019-12-15 05:14:10 UTC  
> Url: https://github.com/boostorg/boostdep/pull/14#issuecomment-565778978  

> This is by design. The module is supposed to be already updated or present before invoking depinst. One (maybe the primary) purpose of depinst is to be used in Boost library CI, such as Travis and Appveyor, and in this case, we already have the library checked out, and we don't want the one from the superproject as it may be older or from a different branch.  
  
Thanks, I understand the point.  
  
I added `--update` option; the argument is updated only when it is given.

---
