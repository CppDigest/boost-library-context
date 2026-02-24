# #363 - boost and cray : `./build.sh: line 15: 42650 Illegal instruction $@` [Open]

> Username: freifrauvonbleifrei  
> Created at: 2018-11-07 10:55:41 UTC  
> Updated at: 2021-06-26 03:09:51 UTC  
> Url: https://github.com/boostorg/build/issues/363  

I tried to build boost 1.68 using the cray compiler on a cray machine. Command:  `boost_1_68_0/tools/build> ./bootstrap.sh --with-toolset=cc` and `boost_1_68_0> ./bootstrap.sh --with-toolset=cc`  
  
but the bootstrap.log shows me an error in both cases:  
```  
./bootstrap/jam0 -f build.jam --toolset=cc --toolset-root= clean  
craylibs//google-perftools/src/memfs_malloc.cc:480] warning: unable to create memfs_malloc_path /var/lib/hugetlbfs/global/pagesize-16777216/hugepagefile.tcmalloc.42650.laiJXC: No such file or directory  
libhugetlbfs [eslogin005:42650]: WARNING: No mount point found for default huge page size. Using first available mount point.  
libhugetlbfs [eslogin005:42650]: WARNING: Hugepage size 2097152 unavailable./build.sh: line 15: 42650 Illegal instruction     (core dumped) $@  
```  
Modules loaded:  
```  
module list  
Currently Loaded Modulefiles:  
  1) modules/3.2.11.1                               16) udreg/2.3.2-6.0.5.0_13.12__ga14955a.ari  
  2) eproxy/2.0.22-6.0.5.0_2.1__g1ebe45c.ari        17) ugni/6.0.14-6.0.5.0_16.9__g19583bb.ari  
  3) cce/8.7.5                                      18) pmi/5.0.14  
  4) craype-haswell                                 19) dmapp/7.1.1-6.0.5.0_49.8__g1125556.ari  
  5) craype-hugepages16M                            20) gni-headers/5.0.12-6.0.5.0_2.15__g2ef1ebc.ari  
  6) craype-network-aries                           21) xpmem/2.2.4-6.0.5.1_8.23__g35d5e73.ari  
  7) craype/2.5.15                                  22) job/2.2.2-6.0.5.0_8.47__g3c644b5.ari  
  8) cray-mpich/7.7.3                               23) dvs/2.7_2.2.66-6.0.5.2_17.4__g99887f3  
  9) moab/9.1.1                                     24) alps/6.5.29-6.0.5.1_3.1__gc22dc90.ari  
 10) torque/6.1.2.h1                                25) rca/2.2.16-6.0.5.0_15.34__g5e09e6d.ari  
 11) system/ws_tools                                26) atp/2.1.3  
 12) system/hlrs-defaults                           27) perftools-base/7.0.4  
 13) cray-python/2.7.13.1                           28) PrgEnv-cray/6.0.4  
 14) python-site/2.7                                29) cray-fftw/3.3.8.1  
 15) cray-libsci/18.07.1                            30) cray-petsc-complex/3.8.4.0  
```  
It does work with the intel-linux compiler though.  
  
It seems from the documentation that the support for the Cray compiler has been discontinued?

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:13 UTC  
> Url: https://github.com/boostorg/build/issues/363#issuecomment-868936383  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
