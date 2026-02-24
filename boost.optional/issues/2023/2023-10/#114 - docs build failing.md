# #114 - docs build failing [Closed]

> Username: sdarwin  
> Created at: 2023-10-04 15:47:12 UTC  
> Updated at: 2023-10-05 15:43:46 UTC  
> Closed at: 2023-10-05 15:43:45 UTC  
> Url: https://github.com/boostorg/optional/issues/114  

See boostorg/boost circleci builds:  
  
https://app.circleci.com/pipelines/github/boostorg/boost  
  
It's not impossible that a docs build could be added in github actions CI. For example the scripts here https://github.com/boostorg/release-tools/tree/develop/build_docs are able to test one library rather than all of boost.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-10-04 17:47:10 UTC  
> Url: https://github.com/boostorg/optional/issues/114#issuecomment-1747369329  

```  
quickbook.quickbook-to-boostbook /root/build/boost/bin.v2/libs/optional/doc/optional.xml  
Generating output file: /root/build/boost/bin.v2/libs/optional/doc/optional.xml  
/root/project/libs/optional/doc/00_optional.qbk:83: error: Unable to find file: 14_io.qbk  
/root/project/libs/optional/doc/00_optional.qbk:84: error: Unable to find file: 15_optional_references.qbk  
/root/project/libs/optional/doc/00_optional.qbk:85: error: Unable to find file: 16_in_place_factories.qbk  
/root/project/libs/optional/doc/00_optional.qbk:86: error: Unable to find file: 17_gotchas.qbk  
/root/project/libs/optional/doc/00_optional.qbk:87: error: Unable to find file: 18_exception_safety.qbk  
/root/project/libs/optional/doc/00_optional.qbk:88: error: Unable to find file: 19_type_requirements.qbk  
/root/project/libs/optional/doc/00_optional.qbk:89: error: Unable to find file: 1A_on_performance.qbk  
Error: Error count: 7.  
  
    "/root/build/dist/bin/quickbook" -I".."     --output-file="/root/build/boost/bin.v2/libs/optional/doc/optional.xml" "/root/project/libs/optional/doc/00_optional.qbk"  
  
...failed quickbook.quickbook-to-boostbook /root/build/boost/bin.v2/libs/optional/doc/optional.xml...  
```  
  
caused by the renumbering in https://github.com/boostorg/optional/commit/6291880ecc58648d6cbca5048f02ad23c2d06899 which wasn't reflected in 00_optional.qbk.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-10-04 17:50:07 UTC  
> Updated at: 2023-10-04 17:50:25 UTC  
> Url: https://github.com/boostorg/optional/issues/114#issuecomment-1747373352  

> It's not impossible that a docs build could be added in github actions CI. For example the scripts here  
  
`b2 libs/optional/doc` should be enough.

---

## Comment 3

> Username: sdarwin  
> Created at: 2023-10-04 18:25:29 UTC  
> Url: https://github.com/boostorg/optional/issues/114#issuecomment-1747422388  

> b2 libs/optional/doc should be enough.  
  
yes, that's true.   
Depending on which boost library it is, running the build in the container `cppalliance/boost_superproject_build:20.04-v2` will ensure pip and gem packages are already installed.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2023-10-04 20:07:32 UTC  
> Url: https://github.com/boostorg/optional/issues/114#issuecomment-1747563588  

Sorry for that. I've just pushed the patch to `develop`.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2023-10-05 15:43:45 UTC  
> Url: https://github.com/boostorg/optional/issues/114#issuecomment-1749163750  

I can see that the docs built successfully in develop.
