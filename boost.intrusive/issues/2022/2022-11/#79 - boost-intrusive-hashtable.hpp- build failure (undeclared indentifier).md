# #79 - boost/intrusive/hashtable.hpp: build failure (undeclared indentifier) [Closed]

> Username: aeolio  
> Created at: 2022-11-06 09:22:29 UTC  
> Updated at: 2022-11-07 08:47:06 UTC  
> Closed at: 2022-11-06 23:16:52 UTC  
> Url: https://github.com/boostorg/intrusive/issues/79  

Hi,  
  
With commit 05bb58091e3f2559e4d3c82b0a013f339c6a6975, major changes were implemented in intrusive/hashtable.cpp.  
  
The changed code was published with Boost 1.80, and starting with this release, build errors can be observed due to an undeclared identifier in line 222 of hashtable.cpp:   
  
```  
In file included from /home/data/test/autobuild/host/mips64-buildroot-linux-gnu/sysroot/usr/include/boost/intrusive/unordered_set.hpp:18,  
                 from ../src/RemoteTagCache.hxx:29,  
                 from ../src/Instance.cxx:30:  
/home/data/test/autobuild/host/mips64-buildroot-linux-gnu/sysroot/usr/include/boost/intrusive/hashtable.hpp: In static member function ‘static std::size_t boost::intrusive::prime_list_holder<Dummy>::position(std::size_t, std::size_t)’:  
/home/data/test/autobuild/host/mips64-buildroot-linux-gnu/sysroot/usr/include/boost/intrusive/hashtable.hpp:222:69: error: ‘sizes’ was not declared in this scope; did you mean ‘size’?  
  222 |          return fastmod_u32(hash, inv_sizes32[size_index], uint32_t(sizes[size_index]));  
      |                                                                     ^~~~~  
      |                                                                     size  
```  
There is no fix in master or development branch yet.   
  
Kind regards,  
Andreas

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-11-06 23:17:49 UTC  
> Url: https://github.com/boostorg/intrusive/issues/79#issuecomment-1304922833  

Many thanks for the report. I hope the patch fixes the problem in develop and we can merge it into master soon.

---

## Comment 2

> Username: aeolio  
> Created at: 2022-11-07 08:47:06 UTC  
> Url: https://github.com/boostorg/intrusive/issues/79#issuecomment-1305272779  

Thank you for the quick solution! The patch fixes this build issue and works on the other platforms (tested: i386, aarch64)
