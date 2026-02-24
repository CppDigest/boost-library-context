# #267 - No libboost_numpy.lib generated when build with python 3.7 [Closed]

> Username: bpkcl630  
> Created at: 2019-05-10 09:51:33 UTC  
> Updated at: 2019-05-10 11:58:35 UTC  
> Closed at: 2019-05-10 11:58:35 UTC  
> Url: https://github.com/boostorg/python/issues/267  

I am using miniconda python 3.7 + msvc 14.2 + win 10.  
When I follow the standard build process to build boost_python. There was no   libboost_numpy library generated.  
  
I  tried python 3.6 , it works well. It seems like the support for python 3.7 has some problem.

---

## Comment 1

> Username: bpkcl630  
> Created at: 2019-05-10 11:51:10 UTC  
> Url: https://github.com/boostorg/python/issues/267#issuecomment-491261719  

It is a bug of anaconda. When run  
`D:/Miniconda3\python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())"`  
returns some error.
