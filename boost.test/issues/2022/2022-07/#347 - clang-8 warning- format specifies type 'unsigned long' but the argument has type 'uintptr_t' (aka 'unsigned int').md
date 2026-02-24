# #347 - clang-8 warning: format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int') [Closed]

> Username: k15tfu  
> Created at: 2022-07-04 16:09:34 UTC  
> Updated at: 2023-06-08 07:14:03 UTC  
> Closed at: 2023-06-08 07:14:03 UTC  
> Url: https://github.com/boostorg/test/issues/347  

Hi!  
  
In file boost/test/impl/execution_monitor.ipp:  
```  
boost/test/impl/execution_monitor.ipp:429:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:434:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:439:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:444:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:450:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:455:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:460:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:465:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:470:48: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          m_sig_info->si_code, (uintptr_t) m_sig_info->si_addr );  
                                               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/test/impl/execution_monitor.ipp:480:27: error:  
      format specifies type 'unsigned long' but the argument has type 'uintptr_t' (aka 'unsigned int')  
      [-Werror,-Wformat]  
                          (uintptr_t) m_sig_info->si_addr );  
                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[...]  
fatal error: too many errors emitted, stopping now [-ferror-limit=]  
```
