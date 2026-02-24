# #82 - Having ode conditionally depend on Boost::MPI (or allow standalone for cmake) [Closed]

> Username: SteveBronder  
> Created at: 2024-06-13 00:04:38 UTC  
> Updated at: 2024-06-19 12:54:22 UTC  
> Closed at: 2024-06-19 12:54:22 UTC  
> Url: https://github.com/boostorg/odeint/issues/82  

odeint's cmake currently requires `Boost::mpi` (see [here](https://github.com/boostorg/odeint/blob/develop/CMakeLists.txt#L24)) and I think a few other boost libraries that much be prebuilt before hand. I think mpi can be kind of a big requirement for a lot of users. It's possible for odeint to be included as a header only library if it is in standalone mode.  
  
Could you add a cmake flag to specify whether the other boost libraries should be included as dependencies? Something like   
  
```cmake  
if (not BOOST_NUMERIC_ODEINT_STANDALONE)  
  target_link_libraries(boost_numeric_odeint ...)  
else()  
  target_compile_definitions(boost_numeric_odeint BOOST_NUMERIC_ODEINT_STANDALONE)  
endif()  
```

---

## Comment 1

> Username: mborland  
> Created at: 2024-06-13 11:17:11 UTC  
> Url: https://github.com/boostorg/odeint/issues/82#issuecomment-2165357669  

It wouldn't be truly standalone because the library has a ton of internal boost dependencies. Would removing all the adapters from `boost/odeint/external/` be sufficient for a standalone mode? Maybe `BOOST_NUMERIC_ODEINT_NO_ADAPTORS`? See current rationale in: https://github.com/boostorg/odeint/pull/64

---

## Comment 2

> Username: SteveBronder  
> Created at: 2024-06-18 15:09:08 UTC  
> Updated at: 2024-06-18 15:57:20 UTC  
> Url: https://github.com/boostorg/odeint/issues/82#issuecomment-2176343757  

I think `BOOST_NUMERIC_ODEINT_NO_ADAPTORS` would make sense as a compile flag.  
  
From reading those comments it looks like odeint would have to be careful that boostdep does not re-add the `Boost::mpi` dependency. @pdimov is there a way to tell boostdep "This file was modified by hand do not overwrite it"? Otherwise it will take a lot of extra care each release.  
  
For background, I'm moving [Stan Math](https://github.com/stan-dev/math) over to use cmake and I'd like to use boosts cmake scheme. We currently use odeint as a header only library from the release version of boost, which is fine and does not depend on the user having mpi. But using the cmake scheme does require mpi which is not super fun. I also fear many of our users just won't have that installed. `Boost::Compute` is also probably an issue.   
  
I think just excluding the adaptors (and their dependencies)** would fix this though. Another fix would be to allow users to specify which adaptors they want and then make them in charge of including the dependencies for those  
  
**EDIT

---

## Comment 3

> Username: pdimov  
> Created at: 2024-06-18 15:38:31 UTC  
> Url: https://github.com/boostorg/odeint/issues/82#issuecomment-2176408493  

`boostdep` never overwrites anything, it just writes to stdout.
