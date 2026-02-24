# #11 - Include version matching for mpi_python [Closed]

> Username: Flamefire  
> Created at: 2019-06-12 13:25:54 UTC  
> Updated at: 2019-06-12 17:15:10 UTC  
> Closed at: 2019-06-12 17:14:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/11  

The FindBoost module states  
  
> Note that Boost Python components require a Python version suffix  
(Boost 1.67 and later), e.g. ``python36`` or ``python27`` for the  
versions built against Python 3.6 and 2.7, respectively.  This also  
applies to additional components using Python including  
``mpi_python`` and ``numpy``.   
  
However in BoostConfig only "^python" is checked which means this fails for mpi_python. Is that intentional?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-06-12 17:15:10 UTC  
> Url: https://github.com/boostorg/boost_install/issues/11#issuecomment-501371260  

Yes, you're right, `mpi_python` should be in the list.
