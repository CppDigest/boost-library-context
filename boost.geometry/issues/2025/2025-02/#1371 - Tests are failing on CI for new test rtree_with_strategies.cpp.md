# #1371 - Tests are failing on CI for new test rtree_with_strategies.cpp [Closed]

> Username: vissarion  
> Created at: 2025-02-04 15:33:46 UTC  
> Updated at: 2025-03-05 16:44:55 UTC  
> Closed at: 2025-03-05 16:44:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1371  

Tests are currently failing on CI for the index test https://github.com/boostorg/geometry/blob/develop/index/test/rtree/rtree_with_strategies.cpp  
  
This is due to long compilation times and/or memory consumption.   
  
Locally, with g++-9 it took 176.39 seconds and ~8.2 GB to compile that test. Similar results with few other versions of clang and g++ so it does not seem as a compiler version issue.   
  
To fix this we can decrease the number of cases tested, split them into files or create separate compilation units in Jamfile. @awulkiew any other ideas?  
  
Interestingly, there are no issues building the same test with cmake on github actions see https://github.com/boostorg/geometry/actions/runs/12876224865/job/35898875915#step:9:125

---

## Comment 1

> Username: awulkiew  
> Created at: 2025-03-04 10:37:09 UTC  
> Updated at: 2025-03-04 10:37:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1371#issuecomment-2697021667  

@vissarion Sorry for late response. There are various geometries, rtree parameters and strategies tested in this one test. I guess it has to be divided into smaller ones. I'll create a PR.
