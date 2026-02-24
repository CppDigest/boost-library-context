# #1055 - fix building with free-threaded python [Closed]

> Username: zhw2101024  
> Created at: 2025-06-24 05:37:43 UTC  
> Updated at: 2025-12-20 00:48:41 UTC  
> Closed at: 2025-12-20 00:48:41 UTC  
> Url: https://github.com/boostorg/boost/issues/1055  

On [free-threaded python](https://docs.python.org/3.14/whatsnew/3.14.html#whatsnew314-pep779) the include files are installed in directory `${PYTHON_ROOT}/python${PYTHON_VERSION}t`, the extra suffix is causing build errors. it seems not too hard to support building boost with free-threaded python.
