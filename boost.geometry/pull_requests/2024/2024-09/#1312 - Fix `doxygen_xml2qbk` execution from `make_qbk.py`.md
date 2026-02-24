# #1312 Fix `doxygen_xml2qbk` execution from `make_qbk.py` [Merged]

> Username: Lastique  
> Created at: 2024-09-24 23:47:44 UTC  
> Updated at: 2024-10-31 15:01:58 UTC  
> Merged at: 2024-09-25 09:28:49 UTC  
> Closed at: 2024-09-25 09:28:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1312  

When `b2` invokes `doc/make_qbk.py`, it passes a relative path to the compiled `doxygen_xml2qbk` executable. This path becomes invalid if used from a directory different from doc, which may happen as `make_qbk.py` changes the current directory during its execution. Additionally, `doc/index/make_qbk.py` invokes `doxygen_xml2qbk` unqualified, which requires the executable to be in `PATH` and is never the case unless the user has pre-compiled and placed it accordingly.  
  
To fix this, first resolve the paths to `doxygen` and `doxygen_xml2qbk` to absolute paths before changing the current directory. Additionally, pass the resolved path to `doxygen_xml2qbk` to `doc/index/make_qbk.py` via the `DOXYGEN_XML2QBK` environment variable.  
  
Fixes https://github.com/boostorg/geometry/issues/1311.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2024-09-25 07:32:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1312#pullrequestreview-2327380151  

Thanks a lot!  
So this fixes a structural issue and makes making documentation easier.  
  
For others, @Lastique also solved the "1.79.2" problem, as discussed here  
https://github.com/boostorg/boostbook/pull/13  
  
For that research, this PR was essential.

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2024-09-25 09:27:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1312#pullrequestreview-2327703795  

Thanks a lot, looks good to me, I approve.   
  
Just a note on documentation, there is a [CI job](https://github.com/boostorg/geometry/actions/workflows/documentation.yml) that builds documentation and seems to work as expected. Of course it is on Ubuntu so it cannot catch issues on Mac/win etc.

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-09-25 12:14:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1312#issuecomment-2373918114  

The `documentation` CI workflow works because it explicitly builds `doxygen_xml2qbk` and copies it to `/usr/local/bin` prior to building the documentation. This is unconventional and should not be required. This PR fixes this, and building docs should now work by simply invoking `b2` in `libs/geometry/doc`. It may be a good idea to update the CI workflow to verify that.

---

## Comment 4

> Username: vissarion  
> Created_at: 2024-09-27 09:23:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1312#issuecomment-2378841698  

> The `documentation` CI workflow works because it explicitly builds `doxygen_xml2qbk` and copies it to `/usr/local/bin` prior to building the documentation. This is unconventional and should not be required. This PR fixes this, and building docs should now work by simply invoking `b2` in `libs/geometry/doc`. It may be a good idea to update the CI workflow to verify that.  
  
That is a good suggestion, I opened a [PR](https://github.com/boostorg/geometry/pull/1314). Please feel free to review it.

---
