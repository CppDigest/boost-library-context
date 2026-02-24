# #152 - Codecov data not syncing [Closed]

> Username: anarthal  
> Created at: 2023-05-22 09:30:27 UTC  
> Updated at: 2024-03-15 18:32:11 UTC  
> Closed at: 2024-03-15 18:32:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/152  

Showing "unusable report"

---

## Comment 1

> Username: anarthal  
> Created at: 2024-03-10 20:28:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/152#issuecomment-1987352881  

Notes:  
* The current workflow uses a codecov uploader built into the Docker container. Since codecov updates the uploader frequently, it may be much wiser to use the GHA codecov upload action, instead.  
* lcov is extremely slow - using plain gcov could make the workflow much faster.  
* It's much easier to use `b2` with `coverage=on` than CMake for this.  
  
We will use this task to rebuild the coverage workflow.
