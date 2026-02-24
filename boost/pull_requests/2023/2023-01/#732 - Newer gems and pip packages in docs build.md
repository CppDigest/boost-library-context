# #732 Newer gems and pip packages in docs build [Merged]

> Username: sdarwin  
> Created at: 2023-01-24 15:04:22 UTC  
> Updated at: 2023-02-03 15:30:30 UTC  
> Merged at: 2023-02-03 15:30:30 UTC  
> Closed at: 2023-02-03 15:30:30 UTC  
> Url: https://github.com/boostorg/boost/pull/732  

From this [Dockerfile](https://github.com/boostorg/release-tools/blob/develop/docker/python3/focal/Dockerfile).  Mainly updating software versions.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2023-02-01 14:30:51 UTC  
> Url: https://github.com/boostorg/boost/pull/732#issuecomment-1412155763  

Hi @mclow could this be merged? Now is a good time in between boost release cycles.  
  
A summary of test results: After upgrading everything, 99% of the boost library documentation continued to build correctly. The only problems were a few formatting issues in gil, hof, and python. They use Sphinx, and the newer version of Sphinx was not entirely compatible. I sent pull requests to those libraries. Gil and Hof have merged the changes. Python is the remaining holdout. At the time of this writing boostorg/python has 29 open pull requests. In that case though with Python, the documentation still compiles, and is mostly fine. Shouldn't be a blocker.

---
