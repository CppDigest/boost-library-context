# #354 - GCC -Wformat warning on 32bit targets [Closed]

> Username: Kojoley  
> Created at: 2022-09-18 19:43:55 UTC  
> Updated at: 2023-06-08 07:14:04 UTC  
> Closed at: 2023-06-08 07:14:04 UTC  
> Url: https://github.com/boostorg/test/issues/354  

I see it on 32bit arm, x86, and mips:  
```  
  gcc.compile.c++ bin.v2/libs/test/build/gcc-10/release/link-static/threading-multi/visibility-hidden/execution_monitor.o  
  In file included from libs/test/src/execution_monitor.cpp:16:  
  ./boost/test/impl/execution_monitor.ipp: In member function 'void boost::detail::system_signal_exception::report() const':  
  ./boost/test/impl/execution_monitor.ipp:428:90: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    428 |                           "signal: illegal opcode; address of failing instruction: 0x%08lx",  
        |                                                                                      ~~~~^  
        |                                                                                          |  
        |                                                                                          long unsigned int  
        |                                                                                      %08x  
  ./boost/test/impl/execution_monitor.ipp:433:88: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    433 |                           "signal: illegal trap; address of failing instruction: 0x%08lx",  
        |                                                                                    ~~~~^  
        |                                                                                        |  
        |                                                                                        long unsigned int  
        |                                                                                    %08x  
  ./boost/test/impl/execution_monitor.ipp:438:95: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    438 |                           "signal: privileged register; address of failing instruction: 0x%08lx",  
        |                                                                                           ~~~~^  
        |                                                                                               |  
        |                                                                                               long unsigned int  
        |                                                                                           %08x  
  ./boost/test/impl/execution_monitor.ipp:443:96: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    443 |                           "signal: internal stack error; address of failing instruction: 0x%08lx",  
        |                                                                                            ~~~~^  
        |                                                                                                |  
        |                                                                                                long unsigned int  
        |                                                                                            %08x  
  ./boost/test/impl/execution_monitor.ipp:449:91: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    449 |                           "signal: illegal operand; address of failing instruction: 0x%08lx",  
        |                                                                                       ~~~~^  
        |                                                                                           |  
        |                                                                                           long unsigned int  
        |                                                                                       %08x  
  ./boost/test/impl/execution_monitor.ipp:454:99: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    454 |                           "signal: illegal addressing mode; address of failing instruction: 0x%08lx",  
        |                                                                                               ~~~~^  
        |                                                                                                   |  
        |                                                                                                   long unsigned int  
        |                                                                                               %08x  
  ./boost/test/impl/execution_monitor.ipp:459:93: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    459 |                           "signal: privileged opcode; address of failing instruction: 0x%08lx",  
        |                                                                                         ~~~~^  
        |                                                                                             |  
        |                                                                                             long unsigned int  
        |                                                                                         %08x  
  ./boost/test/impl/execution_monitor.ipp:464:94: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    464 |                           "signal: co-processor error; address of failing instruction: 0x%08lx",  
        |                                                                                          ~~~~^  
        |                                                                                              |  
        |                                                                                              long unsigned int  
        |                                                                                          %08x  
  ./boost/test/impl/execution_monitor.ipp:469:116: warning: format '%lx' expects argument of type 'long unsigned int', but argument 4 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    469 |                           "signal: SIGILL, si_code: %d (illegal instruction; address of failing instruction: 0x%08lx)",  
        |                                                                                                                ~~~~^  
        |                                                                                                                    |  
        |                                                                                                                    long unsigned int  
        |                                                                                                                %08x  
  ./boost/test/impl/execution_monitor.ipp:479:98: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    479 |                           "signal: integer divide by zero; address of failing instruction: 0x%08lx",  
        |                                                                                              ~~~~^  
        |                                                                                                  |  
        |                                                                                                  long unsigned int  
        |                                                                                              %08x  
  ./boost/test/impl/execution_monitor.ipp:484:92: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    484 |                           "signal: integer overflow; address of failing instruction: 0x%08lx",  
        |                                                                                        ~~~~^  
        |                                                                                            |  
        |                                                                                            long unsigned int  
        |                                                                                        %08x  
  ./boost/test/impl/execution_monitor.ipp:489:105: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    489 |                           "signal: floating point divide by zero; address of failing instruction: 0x%08lx",  
        |                                                                                                     ~~~~^  
        |                                                                                                         |  
        |                                                                                                         long unsigned int  
        |                                                                                                     %08x  
  ./boost/test/impl/execution_monitor.ipp:494:99: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    494 |                           "signal: floating point overflow; address of failing instruction: 0x%08lx",  
        |                                                                                               ~~~~^  
        |                                                                                                   |  
        |                                                                                                   long unsigned int  
        |                                                                                               %08x  
  ./boost/test/impl/execution_monitor.ipp:499:100: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    499 |                           "signal: floating point underflow; address of failing instruction: 0x%08lx",  
        |                                                                                                ~~~~^  
        |                                                                                                    |  
        |                                                                                                    long unsigned int  
        |                                                                                                %08x  
  ./boost/test/impl/execution_monitor.ipp:504:105: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    504 |                           "signal: floating point inexact result; address of failing instruction: 0x%08lx",  
        |                                                                                                     ~~~~^  
        |                                                                                                         |  
        |                                                                                                         long unsigned int  
        |                                                                                                     %08x  
  ./boost/test/impl/execution_monitor.ipp:509:108: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    509 |                           "signal: invalid floating point operation; address of failing instruction: 0x%08lx",  
        |                                                                                                        ~~~~^  
        |                                                                                                            |  
        |                                                                                                            long unsigned int  
        |                                                                                                        %08x  
  ./boost/test/impl/execution_monitor.ipp:514:98: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    514 |                           "signal: subscript out of range; address of failing instruction: 0x%08lx",  
        |                                                                                              ~~~~^  
        |                                                                                                  |  
        |                                                                                                  long unsigned int  
        |                                                                                              %08x  
  In file included from libs/test/src/execution_monitor.cpp:16:  
  ./boost/test/impl/execution_monitor.ipp:519:129: warning: format '%lx' expects argument of type 'long unsigned int', but argument 4 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    519 |                           "signal: SIGFPE, si_code: %d (errnoneous arithmetic operations; address of failing instruction: 0x%08lx)",  
        |                                                                                                                             ~~~~^  
        |                                                                                                                                 |  
        |                                                                                                                                 long unsigned int  
        |                                                                                                                             %08x  
  ./boost/test/impl/execution_monitor.ipp:530:70: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    530 |                           "memory access violation at address: 0x%08lx: no mapping at fault address",  
        |                                                                  ~~~~^  
        |                                                                      |  
        |                                                                      long unsigned int  
        |                                                                  %08x  
  ./boost/test/impl/execution_monitor.ipp:535:70: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    535 |                           "memory access violation at address: 0x%08lx: invalid permissions",  
        |                                                                  ~~~~^  
        |                                                                      |  
        |                                                                      long unsigned int  
        |                                                                  %08x  
  ./boost/test/impl/execution_monitor.ipp:541:100: warning: format '%lx' expects argument of type 'long unsigned int', but argument 4 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    541 |                           "signal: SIGSEGV, si_code: %d (memory access violation at address: 0x%08lx)",  
        |                                                                                                ~~~~^  
        |                                                                                                    |  
        |                                                                                                    long unsigned int  
        |                                                                                                %08x  
  ./boost/test/impl/execution_monitor.ipp:552:70: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    552 |                           "memory access violation at address: 0x%08lx: invalid address alignment",  
        |                                                                  ~~~~^  
        |                                                                      |  
        |                                                                      long unsigned int  
        |                                                                  %08x  
  ./boost/test/impl/execution_monitor.ipp:557:70: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    557 |                           "memory access violation at address: 0x%08lx: non-existent physical address",  
        |                                                                  ~~~~^  
        |                                                                      |  
        |                                                                      long unsigned int  
        |                                                                  %08x  
  ./boost/test/impl/execution_monitor.ipp:562:70: warning: format '%lx' expects argument of type 'long unsigned int', but argument 3 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    562 |                           "memory access violation at address: 0x%08lx: object specific hardware error",  
        |                                                                  ~~~~^  
        |                                                                      |  
        |                                                                      long unsigned int  
        |                                                                  %08x  
  ./boost/test/impl/execution_monitor.ipp:568:100: warning: format '%lx' expects argument of type 'long unsigned int', but argument 4 has type 'uintptr_t' {aka 'unsigned int'} [-Wformat=]  
    568 |                           "signal: SIGSEGV, si_code: %d (memory access violation at address: 0x%08lx)",  
        |                                                                                                ~~~~^  
        |                                                                                                    |  
        |                                                                                                    long unsigned int  
        |                                                                                                %08x  
```
