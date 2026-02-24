# #155 PEP 328 import [Merged]

> Username: SoapGentoo  
> Created at: 2024-01-24 17:47:28 UTC  
> Updated at: 2024-03-08 10:28:52 UTC  
> Merged at: 2024-03-08 09:47:48 UTC  
> Closed at: 2024-03-08 09:47:48 UTC  
> Url: https://github.com/boostorg/mpi/pull/155  

Hi @aminiussi   
we have been carrying this patch in Gentoo for ages, and would like to upstream it. The current import statements are still from the Python 2/pre-PEP328 days. In Gentoo, the Boost.MPI python layout looks like:  
```  
$ tree /usr/lib/python3.11/site-packages/boost/  
/usr/lib/python3.11/site-packages/boost/  
├── __init__.py  
├── __pycache__  
│   ├── __init__.cpython-311.opt-1.pyc  
│   ├── __init__.cpython-311.opt-2.pyc  
│   └── __init__.cpython-311.pyc  
└── mpi.so  
  
2 directories, 5 files  
```  
and in order for `__init__.py` to import the CPython module, you need to use PEP328-style absolute imports on modern python installations.

---

## Comment 1

> Username: SoapGentoo  
> Created_at: 2024-03-06 17:50:49 UTC  
> Url: https://github.com/boostorg/mpi/pull/155#issuecomment-1981466020  

@aminiussi ping

---

## Comment 2

> Username: aminiussi  
> Created_at: 2024-03-08 09:50:35 UTC  
> Url: https://github.com/boostorg/mpi/pull/155#issuecomment-1985382012  

I accepted the merge to develop. As I do not really use Python myself anymore, I'll merge develop to master after 1.85 so that it can be checked by more knowledgeable.  
The change seems safe and python 3.x is the default anyway.

---
