# #388 - Improve doxygen files clean-up [Open]

> Username: mloskot  
> Created at: 2017-03-28 20:14:06 UTC  
> Updated at: 2024-10-02 10:42:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/388  

Task to improve changes from PR #387  
  
Below are suggestions from @awulkiew which I intend to work on:  
  
------  
  
Instead of clearing out the index dir in the `index/make_qbk.py` and doing the same in the `make_qbk.py`, `index/make_qbk.py` could be executed with `--release-build` option and cleanup related to the index could be done only there.  
  
There are generated `*.qbk` files in directories doc/generated and doc/index/generated which are not needed. Also `doc/html/geometry_HTML.manifest` file. Ideally they probably should be removed as well, but this should be done after `b2` is called. But since `b2` can run `make_qbk.py` and the script is then executed before quickbook it is impossible to implement the cleanup of generated qbk files in this single script. Maybe it could be done in a separate cleanup script run by b2 after building docs from qbk files, but I don't know how exactly.  
  
------
