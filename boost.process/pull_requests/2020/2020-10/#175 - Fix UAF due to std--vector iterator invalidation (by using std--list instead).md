# #175 Fix UAF due to std::vector iterator invalidation (by using std::list instead) [Closed]

> Username: azat  
> Created at: 2020-10-08 20:38:32 UTC  
> Updated at: 2023-02-06 12:05:21 UTC  
> Closed at: 2023-02-06 12:05:21 UTC  
> Url: https://github.com/boostorg/process/pull/175  

Here is a stacktrace from the valgrind (stripped, since w/o stripping  
you need 10 monitors to see it):  
  
    ==4127429== Invalid write of size 4  
    ==4127429==    at 0x4BD4401: boost::process::detail::posix::sigchld_service::_handle_signal() (sigchld_service.hpp:104)  
    ==4127429==    by 0x4BFF09B: boost::asio::async_result<>::return_type boost::process::detail::posix::sigchld_service::async_wait<>()::{}::operator()() const::{}::operator()(boost::system::error_code, int) const::{}::operator()() const (sigchld_service.hpp:60)  
    ==4127429==    by 0x4C13A1E: void boost::asio::asio_handler_invoke<>() (handler_invoke_hook.hpp:69)  
    ==4127429==    by 0x4C137AD: void boost_asio_handler_invoke_helpers::invoke<>() (handler_invoke_helpers.hpp:37)  
    ==4127429==    by 0x4C13272: _ZNK5boost4asio15system_executor8dispatchIZZZNS_7process6detail5posix15sigchld_service10async_waitIZNS5_14io_context_ref10on_successINS5_8executorINS_6fusion10joint_viewINSB_5tupleIJNS5_12exe_cmd_initIcEES8_EEENSB_11filter_viewIKNSD_IJRNS_10filesystem4pathERSt6vectorINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEESaISR_EERNS5_8pipe_outILi2ELin1EEERNS0_10io_contextERKNS5_10use_vfork_ERNS5_8on_exit_EEEENS4_14is_initializerIN4mpl_3argILin1EEEEEEEEEEEEEvRT_EUliRKSt10error_codeE_EENS0_12async_resultINSt5decayIS1F_E4typeEFviS1H_EE11return_typeEiOS1F_ENKUlvE_clEvENKUlRKNS_6system10error_codeEiE_clES1X_iEUlvE_SaIvEEEvS1S_RKT0_ (system_executor.hpp:39)  
    ==4127429==    by 0x4C12C4B: boost::asio::detail::work_dispatcher<>::operator()() (work_dispatcher.hpp:59)  
    ==4127429==    by 0x4C11E86: void boost::asio::asio_handler_invoke<>() (handler_invoke_hook.hpp:69)  
    ==4127429==    by 0x4C0FD8E: void boost_asio_handler_invoke_helpers::invoke<>() (handler_invoke_helpers.hpp:37)  
    ==4127429==    by 0x4C120BC: void boost::asio::detail::handler_work<>::complete<>() (handler_work.hpp:100)  
    ==4127429==    by 0x4C0FF59: boost::asio::detail::completion_handler<>::do_complete() (completion_handler.hpp:70)  
    ==4127429==    by 0x4C0E730: void boost::asio::detail::strand_service::dispatch<>() (strand_service.hpp:88)  
    ==4127429==    by 0x4C0B6BC: _ZNK5boost4asio10io_context6strand8dispatchINS0_6detail15work_dispatcherIZZZNS_7process6detail5posix15sigchld_service10async_waitIZNS8_14io_context_ref10on_successINS8_8executorINS_6fusion10joint_viewINSE_5tupleIJNS8_12exe_cmd_initIcEESB_EEENSE_11filter_viewIKNSG_IJRNS_10filesystem4pathERSt6vectorINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEESaISU_EERNS8_8pipe_outILi2ELin1EEERS1_RKNS8_10use_vfork_ERNS8_8on_exit_EEEENS7_14is_initializerIN4mpl_3argILin1EEEEEEEEEEEEEvRT_EUliRKSt10error_codeE_EENS0_12async_resultINSt5decayIS1H_E4typeEFviS1J_EE11return_typeEiOS1H_ENKUlvE_clEvENKUlRKNS_6system10error_codeEiE_clES1Z_iEUlvE_EESaIvEEEvS1U_RKT0_ (io_context_strand.hpp:160)  
  
    ==4127429==  Address 0x86d6aa8 is 40 bytes inside a block of size 160 free'd  
    ==4127429==    at 0x483C08B: operator delete(void*, unsigned long) (vg_replace_malloc.c:593)  
    ==4127429==    by 0x4BE4EC3: __gnu_cxx::new_allocator<>::deallocate() (new_allocator.h:133)  
    ==4127429==    by 0x4BE13DD: std::allocator_traits<>::deallocate() (alloc_traits.h:492)  
    ==4127429==    by 0x4BDCBFD: std::_Vector_base<>::_M_deallocate() (stl_vector.h:354)  
    ==4127429==    by 0x4C05372: void std::vector<>::_M_realloc_insert<>() (vector.tcc:500)  
    ==4127429==    by 0x4C01A3F: std::pair<>& std::vector<>::emplace_back<>() (vector.tcc:121)  
    ==4127429==    by 0x4BFEFE4: boost::asio::async_result<>::return_type boost::process::detail::posix::sigchld_service::async_wait<>()::{}::operator()() const (sigchld_service.hpp:62)  
    ==4127429==    by 0x4C13BA9: void boost::asio::asio_handler_invoke<>() (handler_invoke_hook.hpp:69)  
    ==4127429==    by 0x4C1393D: _ZN33boost_asio_handler_invoke_helpers6invokeIZN5boost7process6detail5posix15sigchld_service10async_waitIZNS4_14io_context_ref10on_successINS4_8executorINS1_6fusion10joint_viewINSA_5tupleIJNS4_12exe_cmd_initIcEES7_EEENSA_11filter_viewIKNSC_IJRNS1_10filesystem4pathERSt6vectorINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEESaISQ_EERNS4_8pipe_outILi2ELin1EEERNS1_4asio10io_contextERKNS4_10use_vfork_ERNS4_8on_exit_EEEENS3_14is_initializerIN4mpl_3argILin1EEEEEEEEEEEEEvRT_EUliRKSt10error_codeE_EENSX_12async_resultINSt5decayIS1F_E4typeEFviS1H_EE11return_typeEiOS1F_EUlvE_S1T_EEvS1G_RT0_ (handler_invoke_helpers.hpp:37)  
    ==4127429==    by 0x4C1354F: _ZNK5boost4asio15system_executor8dispatchIZNS_7process6detail5posix15sigchld_service10async_waitIZNS5_14io_context_ref10on_successINS5_8executorINS_6fusion10joint_viewINSB_5tupleIJNS5_12exe_cmd_initIcEES8_EEENSB_11filter_viewIKNSD_IJRNS_10filesystem4pathERSt6vectorINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEESaISR_EERNS5_8pipe_outILi2ELin1EEERNS0_10io_contextERKNS5_10use_vfork_ERNS5_8on_exit_EEEENS4_14is_initializerIN4mpl_3argILin1EEEEEEEEEEEEEvRT_EUliRKSt10error_codeE_EENS0_12async_resultINSt5decayIS1F_E4typeEFviS1H_EE11return_typeEiOS1F_EUlvE_SaIvEEEvS1S_RKT0_ (system_executor.hpp:39)  
    ==4127429==    by 0x4C12FFB: boost::asio::detail::work_dispatcher<>::operator()() (work_dispatcher.hpp:59)  
    ==4127429==    by 0x4C1259F: void boost::asio::asio_handler_invoke<>() (handler_invoke_hook.hpp:69)  
  
    ==4127429==  Block was alloc'd at  
    ==4127429==    at 0x483ADEF: operator new(unsigned long) (vg_replace_malloc.c:342)  
    ==4127429==    by 0x4C0EA1B: __gnu_cxx::new_allocator<>::allocate(unsigned long, void const*) (new_allocator.h:115)  
    ==4127429==    by 0x4C0B94F: std::allocator_traits<>::allocate(std::allocator<std::pair<int, std::function<void (int, std::error_code)> > >&, unsigned long) (alloc_traits.h:460)  
    ==4127429==    by 0x4C07EFF: std::_Vector_base<>::_M_allocate(unsigned long) (stl_vector.h:346)  
    ==4127429==    by 0x4C0526E: void std::vector<>::_M_realloc_insert<>() (vector.tcc:440)  
    ==4127429==    by 0x4C01A3F: std::pair<>& std::vector<>::emplace_back<>() (vector.tcc:121)  
    ==4127429==    by 0x4BFEFE4: boost::asio::async_result<>::return_type boost::process::detail::posix::sigchld_service::async_wait<>()::{}::operator()() const (sigchld_service.hpp:62)  
    ==4127429==    by 0x4C13BA9: void boost::asio::asio_handler_invoke<>() (handler_invoke_hook.hpp:69)  
    ==4127429==    by 0x4C1393D: _ZN33boost_asio_handler_invoke_helpers6invokeIZN5boost7process6detail5posix15sigchld_service10async_waitIZNS4_14io_context_ref10on_successINS4_8executorINS1_6fusion10joint_viewINSA_5tupleIJNS4_12exe_cmd_initIcEES7_EEENSA_11filter_viewIKNSC_IJRNS1_10filesystem4pathERSt6vectorINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEESaISQ_EERNS4_8pipe_outILi2ELin1EEERNS1_4asio10io_contextERKNS4_10use_vfork_ERNS4_8on_exit_EEEENS3_14is_initializerIN4mpl_3argILin1EEEEEEEEEEEEEvRT_EUliRKSt10error_codeE_EENSX_12async_resultINSt5decayIS1F_E4typeEFviS1H_EE11return_typeEiOS1F_EUlvE_S1T_EEvS1G_RT0_ (handler_invoke_helpers.hpp:37)  
    ==4127429==    by 0x4C1354F: _ZNK5boost4asio15system_executor8dispatchIZNS_7process6detail5posix15sigchld_service10async_waitIZNS5_14io_context_ref10on_successINS5_8executorINS_6fusion10joint_viewINSB_5tupleIJNS5_12exe_cmd_initIcEES8_EEENSB_11filter_viewIKNSD_IJRNS_10filesystem4pathERSt6vectorINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEESaISR_EERNS5_8pipe_outILi2ELin1EEERNS0_10io_contextERKNS5_10use_vfork_ERNS5_8on_exit_EEEENS4_14is_initializerIN4mpl_3argILin1EEEEEEEEEEEEEvRT_EUliRKSt10error_codeE_EENS0_12async_resultINSt5decayIS1F_E4typeEFviS1H_EE11return_typeEiOS1F_EUlvE_SaIvEEEvS1S_RKT0_ (system_executor.hpp:39)  
    ==4127429==    by 0x4C12FFB: boost::asio::detail::work_dispatcher<>::operator()() (work_dispatcher.hpp:59)  
    ==4127429==    by 0x4C1259F: void boost::asio::asio_handler_invoke<>() (handler_invoke_hook.hpp:69)

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2020-10-08 23:48:05 UTC  
> Url: https://github.com/boostorg/process/pull/175#issuecomment-705886320  

Can you reproduce the issue or is that just based on the valgrind report?

---

## Comment 2

> Username: azat  
> Created_at: 2020-10-09 12:45:43 UTC  
> Url: https://github.com/boostorg/process/pull/175#issuecomment-706159699  

>Can you reproduce the issue or is that just based on the valgrind report?  
  
Yes this is a reproducible problem, but I can't provide a test right now.  
  
AFAICS The problem is that during iteration over `_receivers` it can go to `async_wait` and call `emplace_back` which will invalidate memory of the current element.  
  
```  
(gdb) bt  
0  boost::process::detail::posix::sigchld_service::async_wait<>()::{lambda()1}::operator()() const (this=0x7ffff562c600) at sigchld_service.hpp:62  
...  
13 0x00007ffff7ea6faa in boost::process::detail::posix::sigchld_service::async_wait<>() (this=0x7ffff562c600, pid=531583, handler=...) at sigchld_service.hpp:43  
...  
45 0x00007ffff7e7b863 in boost::process::detail::posix::sigchld_service::_handle_signal (this=0x7ffff562c600, ec=...) at sigchld_service.hpp:103  
46 0x00007ffff7ea6d2c in boost::process::detail::posix::sigchld_service::async_wait<>()::{lambda()1}::operator()() const::{lambda(boost::system::error_code const&, int)1}::operator()(boost::system::error_code, int) const::{lambda()1}::operator()() const (this=0x7ffff562c600) at sigchld_service.hpp:60  
...  
61 0x00007ffff7ea6cef in boost::process::detail::posix::sigchld_service::async_wait<>()::{lambda()1}::operator()() const::{lambda(boost::system::error_code const&, int)1}::operator()(boost::system::error_code, int) const (this=0x7ffff562c600, ec=...) at sigchld_service.hpp:60  
...  
106 0x00007ffff610d293 in clone () from /usr/lib/libc.so.6  
```  
  
While `std::list` don't invalidate it, since it don't reallocate memory.

---

## Comment 3

> Username: azat  
> Created_at: 2020-10-21 10:12:54 UTC  
> Url: https://github.com/boostorg/process/pull/175#issuecomment-713463289  

@klemens-morgenstern friendly ping

---

## Comment 4

> Username: dkl  
> Created_at: 2020-12-14 14:10:23 UTC  
> Url: https://github.com/boostorg/process/pull/175#issuecomment-744465337  

(linking to issue #100 since it's related to this)

---

## Comment 5

> Username: azat  
> Created_at: 2021-10-14 16:22:34 UTC  
> Url: https://github.com/boostorg/process/pull/175#issuecomment-943515613  

Closed in favor of 22c5ed1a41b623ead8bb81a0fa8534a4004878d5

---

## Comment 6

> Username: azat  
> Created_at: 2021-10-14 16:23:43 UTC  
> Url: https://github.com/boostorg/process/pull/175#issuecomment-943516642  

The patch hadn't been merged yet, reopen.  
@klemens-morgenstern what is the status of this patch or your patch from signal-set branch?

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2022-12-13 01:27:23 UTC  
> Url: https://github.com/boostorg/process/pull/175#issuecomment-1347616242  

I had to move a few things around due to changes in asio last release. I don' t really understand the bug, is it still there?

---

## Comment 8

> Username: dkl  
> Created_at: 2022-12-14 11:48:09 UTC  
> Url: https://github.com/boostorg/process/pull/175#issuecomment-1351137687  

Yes, this issue still exists. The problem is that the `_receivers` vector in `sigchld_service` is reallocated by `sigchld_service::async_wait()` while being iterated in `sigchld_service::_handle_signal()`. This leads to use-after-free and undefined behaviour and crashes later.  
  
Issue #100 has a test program that triggers this. Without this fix, it crashes for me on Linux, with boost 1.80.0, but also with boost-process master or develop branches. With this fix, it works. I don't see the bug in valgrind, but `-fsanitize=address` finds it. There are other ways to fix it too, for example: https://github.com/dkl/boost-process/commit/211b100a9f9de902f2badfaa6d05e98ca87c84de

---
