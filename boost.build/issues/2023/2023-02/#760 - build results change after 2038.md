# #760 - build results change after 2038 [Closed]

> Username: bmwiedemann  
> Created at: 2023-02-19 19:22:23 UTC  
> Updated at: 2023-02-19 20:10:00 UTC  
> Closed at: 2023-02-19 20:09:59 UTC  
> Url: https://github.com/boostorg/build/issues/760  

While working on [reproducible builds](https://reproducible-builds.org/) for [openSUSE](https://en.opensuse.org/openSUSE:Reproducible_Builds), I found that  
our `boost_1_81_0` package produced slightly different binaries when built on x86_64 after 2038 (compared to now or 2037 which are fully reproducible)  
  
This can indicate that somewhere in [the complex build logic](https://code.opensuse.org/package/boost/blob/409497cb02b4ada310514344054f16280ca3b436/f/boost.spec#_1282), a 32-bit signed integer UNIX timestamp is used and overflowing after 2038-01-18  
  
```diff  
--- old /usr/lib64/libboost_log.so.1.81.0 (objdump)  
+++ new /usr/lib64/libboost_log.so.1.81.0 (objdump)  
@@ -6824,9 +6824,9 @@  
  1acf8 77726170 70657249 4e53315f 31367265  wrapperINS1_16re  
  1ad08 736f6c76 65725f73 65727669 6365494e  solver_serviceIN  
  1ad18 53305f32 69703375 64704545 45454500  S0_2ip3udpEEEEE.  
- 1ad28 6c696262 6f6f7374 5f746872 6561642e  libboost_thread.  
- 1ad38 736f2e31 2e38312e 30006c69 62626f6f  so.1.81.0.libboo  
- 1ad48 73745f66 696c6573 79737465 6d2e736f  st_filesystem.so  
+ 1ad28 6c696262 6f6f7374 5f66696c 65737973  libboost_filesys  
+ 1ad38 74656d2e 736f2e31 2e38312e 30006c69  tem.so.1.81.0.li  
+ 1ad48 62626f6f 73745f74 68726561 642e736f  bboost_thread.so  
  1ad58 2e312e38 312e3000 6c696273 7464632b  .1.81.0.libstdc+  
```

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2023-02-19 20:09:58 UTC  
> Url: https://github.com/boostorg/build/issues/760#issuecomment-1436081768  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
