# #243 - clang++-19 errors on segment_manager.hpp [Closed]

> Username: eddelbuettel  
> Created at: 2024-12-20 13:25:22 UTC  
> Updated at: 2025-04-15 12:26:24 UTC  
> Closed at: 2025-04-15 12:26:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/243  

The [CRAN][cran] repository (of packages for the R language) found that after the Boost 1.87.0 update, previously working now errors.  (I maintain [BH][bh], a package of a large-ish subset of Boost as a header-only library, this is used fairly widely by about 335 [CRAN][cran] package and a further 40 [BioConductor][bioc] packages.  One of those, [BiocParallel][biocpara] failed.)  
  
We can reproduce the error on a standard Ubuntu 24.04 LTS installation with `clang++-19` using one of the example programs:  
  
```sh  
$ wget -q https://raw.githubusercontent.com/boostorg/interprocess/refs/heads/develop/example/doc_managed_heap_memory.cpp   
$ clang++-19 -I'/usr/local/lib/R/site-library/BH/include'  -fpic  -Wall -O3 -pedantic -o doc_managed_heap_memory doc_managed_heap_memory.cpp   
In file included from doc_managed_heap_memory.cpp:14:  
In file included from /usr/local/lib/R/site-library/BH/include/boost/interprocess/managed_heap_memory.hpp:27:  
In file included from /usr/local/lib/R/site-library/BH/include/boost/interprocess/detail/managed_memory_impl.hpp:30:  
/usr/local/lib/R/site-library/BH/include/boost/interprocess/segment_manager.hpp:1055:41: error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
 1055 |          hdr = block_header_t::template from_first_header(reinterpret_cast<index_data_t*>((void*)((char*)buffer_ptr + front_space)));  
      |                                         ^  
1 error generated.  
$ clang++-19 --version  
Ubuntu clang version 19.1.1 (1ubuntu1~24.04.2)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/lib/llvm-19/bin  
$   
```  
  
Note that I point into the Boost 1.87.0 subset packaged as R package [BH][bh] here which [BiocParallel][biocpara] built against all prior versions.  
  
Adding the compiler argument permits a compilation but it would be preferable if this was not required:  
  
```sh  
$ clang++-19 -I'/usr/local/lib/R/site-library/BH/include' -Wno-missing-template-arg-list-after-template-kw -fpic  -Wall -O3 -pedantic -o doc_managed_heap_memory doc_managed_heap_memory.cpp   
$ ./doc_managed_heap_memory   
$   
```  
  
[cran]: https://cran.r-project.org  
[bh]: https://cran.r-project.org/package=BH  
[bioc]: https://www.bioconductor.org  
[biocpara]: https://www.bioconductor.org/packages/release/bioc/html/BiocParallel.html

---

## Comment 1

> Username: nega0  
> Created at: 2025-04-03 21:42:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/243#issuecomment-2776978812  

And now XCode 16.3   
```  
Apple clang version 17.0.0 (clang-1700.0.13.3)  
Target: arm64-apple-darwin24.5.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
```  
  
[Boost Regression](https://regression.boost.io/master/developer/output/teeks99-dkr-arm64-mc19-23-boost-bin-v2-libs-interprocess-test-segment_manager_test-test-clang-linux-19~c++23-debug-arm_64-debug-symbols-off-threading-multi-visibility-hidden.html)  
  
Relevant patch to clang - https://lists.llvm.org/pipermail/cfe-commits/Week-of-Mon-20240527/583746.html  
  
Clang 19 Release notes- https://releases.llvm.org/19.1.0/tools/clang/docs/ReleaseNotes.html#resolutions-to-c-defect-reports

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-04-15 12:26:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/243#issuecomment-2804883214  

Many thanks for the report. Fixed in develop.
