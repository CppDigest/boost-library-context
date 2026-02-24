# #44 - Compatibility with pybind11? [Closed]

> Username: danzimmerman  
> Created at: 2020-08-13 02:52:31 UTC  
> Updated at: 2020-08-13 23:45:31 UTC  
> Closed at: 2020-08-13 23:45:31 UTC  
> Url: https://github.com/boostorg/dll/issues/44  

I've got a library compiled to a DLL that works properly in C++ when loaded with `boost::dll`.  
  
I'd like to load the same binary in Python using `pybind11`, which works fine by itself. However, the DLL compiled with a `PYBIND11_MODULE`  in addition to a `BOOST_DLL_ALIAS` fails to load in C++ with `boost::dll`.  
  
Throws `boost::dll::shared_library::load() failed: The specified module could not be found`.  
  
The library is for use from C++ but needs some complex functional testing against Python prototype code. It's convenient to  imagine a dual-use Python/C++ DLL that has both interfaces in a single binary build.  
  
But maybe this isn't possible?   
  
I've put a detailed description in this [gist](https://gist.github.com/danzimmerman/62aac05da80a1d9097b8590be76830ff).

---

## Comment 1

> Username: danzimmerman  
> Created at: 2020-08-13 18:26:02 UTC  
> Url: https://github.com/boostorg/dll/issues/44#issuecomment-673638538  

Minimal-ish example here:  
  
https://github.com/danzimmerman/pybind-boostdll-minimal

---

## Comment 2

> Username: danzimmerman  
> Created at: 2020-08-13 23:45:31 UTC  
> Url: https://github.com/boostorg/dll/issues/44#issuecomment-673763338  

Not a boost issue at all... needs python DLL to run in C++. Sorry for the noise.
