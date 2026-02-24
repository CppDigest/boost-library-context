# #46 - convert_test_performance failed to link for <link>shared [Closed]

> Username: BinCaoWR  
> Created at: 2019-06-21 05:48:06 UTC  
> Updated at: 2019-06-21 09:53:18 UTC  
> Closed at: 2019-06-21 09:53:17 UTC  
> Url: https://github.com/boostorg/convert/issues/46  

clang-vxworks.link ../bin.v2/libs/convert/test/clang-vxworks/debug/cross-compile-vxworks/convert_test_performance.vxe  
/llvm-8.0.0.1/LINUX64/bin/ldpentium: warning: ../bin.v2/libs/chrono/build/clang-vxworks/debug/cross-compile-vxworks/libboost_chrono.so.1.70.0, needed by ../bin.v2/libs/timer/build/clang-vxworks/debug/cross-compile-vxworks/libboost_timer.so.1.70.0, not found (try using -rpath or -rpath-link)  
/llvm-8.0.0.1/LINUX64/bin/ldpentium: ../bin.v2/libs/timer/build/clang-vxworks/debug/cross-compile-vxworks/libboost_timer.so.1.70.0: undefined reference to `boost::chrono::steady_clock::now()'  
  
 /llvm-8.0.0.1/LINUX64/bin/ldpentium -m elf_x86_64 -L"/usr/lib/common" -o "../bin.v2/libs/convert/test/clang-vxworks/debug/cross-compile-vxworks/convert_test_performance.vxe" --defsym __wrs_rtp_base=0x200000 -u __wr_need_frame_add -u __tls__ -T/usr/ldscripts/rtp.ld /usr/lib/common//crt0.o "../bin.v2/libs/convert/test/clang-vxworks/debug/cross-compile-vxworks/performance.o" "../bin.v2/libs/timer/build/clang-vxworks/debug/cross-compile-vxworks/libboost_timer.so.1.70.0" --as-needed -E --start-group -lunix -lnet  -lc  -lc_internal  -lllvm  -lcplusplus  -lllvmcplus  -ldl     --end-group       
  
...failed clang-vxworks.link ../bin.v2/libs/convert/test/clang-vxworks/debug/cross-compile-vxworks/convert_test_performance.vxe...  
  
The link can succeed after I updated following line in libs/convert/test/jamfile.v2.  
  
From:  
exe convert_test_performance : performance.cpp /boost/timer//boost_timer ;  
To:  
exe convert_test_performance : performance.cpp /boost/timer//boost_timer /boost/chrono//boost_chrono ;

---

## Comment 1

> Username: yet-another-user  
> Created at: 2019-06-21 06:14:53 UTC  
> Url: https://github.com/boostorg/convert/issues/46#issuecomment-504304237  

Thank you for your input. Much appreciated. I admit I know nothing about jamfile. Someone from the infrastructure management team added it. If you could provide a merge request, I'd gladly merge. Thanks in advance.

---

## Comment 2

> Username: BinCaoWR  
> Created at: 2019-06-21 06:45:09 UTC  
> Url: https://github.com/boostorg/convert/issues/46#issuecomment-504311777  

Glad to do. The pull request created.

---

## Comment 3

> Username: yet-another-user  
> Created at: 2019-06-21 09:53:17 UTC  
> Url: https://github.com/boostorg/convert/issues/46#issuecomment-504367137  

Much appreciated. Thanks again.
