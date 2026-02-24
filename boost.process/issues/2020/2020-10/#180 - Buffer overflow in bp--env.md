# #180 - Buffer overflow in bp::env [Open]

> Username: ingomueller-net  
> Created at: 2020-10-29 16:29:07 UTC  
> Updated at: 2021-01-19 09:54:49 UTC  
> Url: https://github.com/boostorg/process/issues/180  

[AddressSanitizer](https://github.com/google/sanitizers/wiki/AddressSanitizer) reports a `heap-buffer-overflow` on the following program in Boost version 1.74:  
  
```cpp  
#include <boost/process.hpp>  
  
namespace bp = boost::process;  
  
int main(int, char**) {  
    bp::system("/usr/bin/env", bp::env["VALUE_VALUE_VALUE_111VALUE_1"]="foo", bp::env["VALUE_2"]+={"bar1", "bar2"});  
}  
```  
  
The example is taken from the [tutorial](https://www.boost.org/doc/libs/1_74_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.env) but uses a longer variable name. (The original example does not experience the problem.)  
  
Compile and run like so (assuming above code is in `testbp.cpp`, adapt paths if necessary):  
  
```  
clang++-9.0 testbp.cpp -std=c++17 -o testbp -I /opt/boost-1.74.0/include/ -fsanitize=address,undefined -fno-omit-frame-pointer  
./testbp  
```  
  
The full output on my machine is:  
  
```  
==8012==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x603000000054 at pc 0x000000439152 bp 0x7ffd8f5f7750 sp 0x7ffd8f5f6ef8  
READ of size 29 at 0x603000000054 thread T0  
    #0 0x439151  (/home/muellein/git/jitq/backend/build/testbp+0x439151)  
    #1 0x4394fa  (/home/muellein/git/jitq/backend/build/testbp+0x4394fa)  
    #2 0x504b9d  (/home/muellein/git/jitq/backend/build/testbp+0x504b9d)  
    #3 0x504960  (/home/muellein/git/jitq/backend/build/testbp+0x504960)  
    #4 0x5040e4  (/home/muellein/git/jitq/backend/build/testbp+0x5040e4)  
    #5 0x5032cf  (/home/muellein/git/jitq/backend/build/testbp+0x5032cf)  
    #6 0x50286a  (/home/muellein/git/jitq/backend/build/testbp+0x50286a)  
    #7 0x500ce0  (/home/muellein/git/jitq/backend/build/testbp+0x500ce0)  
    #8 0x500336  (/home/muellein/git/jitq/backend/build/testbp+0x500336)  
    #9 0x4fa39d  (/home/muellein/git/jitq/backend/build/testbp+0x4fa39d)  
    #10 0x4f9abc  (/home/muellein/git/jitq/backend/build/testbp+0x4f9abc)  
    #11 0x4f96e2  (/home/muellein/git/jitq/backend/build/testbp+0x4f96e2)  
    #12 0x4f93fa  (/home/muellein/git/jitq/backend/build/testbp+0x4f93fa)  
    #13 0x4eb37c  (/home/muellein/git/jitq/backend/build/testbp+0x4eb37c)  
    #14 0x4ea490  (/home/muellein/git/jitq/backend/build/testbp+0x4ea490)  
    #15 0x4e9f43  (/home/muellein/git/jitq/backend/build/testbp+0x4e9f43)  
    #16 0x4e9206  (/home/muellein/git/jitq/backend/build/testbp+0x4e9206)  
    #17 0x4e8af5  (/home/muellein/git/jitq/backend/build/testbp+0x4e8af5)  
    #18 0x4d06b7  (/home/muellein/git/jitq/backend/build/testbp+0x4d06b7)  
    #19 0x4cc025  (/home/muellein/git/jitq/backend/build/testbp+0x4cc025)  
    #20 0x7ff8ad2f209a  (/lib/x86_64-linux-gnu/libc.so.6+0x2409a)  
    #21 0x421d79  (/home/muellein/git/jitq/backend/build/testbp+0x421d79)  
  
0x603000000054 is located 0 bytes to the right of 20-byte region [0x603000000040,0x603000000054)  
allocated by thread T0 here:  
    #0 0x4c93dd  (/home/muellein/git/jitq/backend/build/testbp+0x4c93dd)  
    #1 0x4e35ef  (/home/muellein/git/jitq/backend/build/testbp+0x4e35ef)  
    #2 0x4e3400  (/home/muellein/git/jitq/backend/build/testbp+0x4e3400)  
    #3 0x4e2eb2  (/home/muellein/git/jitq/backend/build/testbp+0x4e2eb2)  
    #4 0x4e27fe  (/home/muellein/git/jitq/backend/build/testbp+0x4e27fe)  
    #5 0x4e25a6  (/home/muellein/git/jitq/backend/build/testbp+0x4e25a6)  
    #6 0x4e2327  (/home/muellein/git/jitq/backend/build/testbp+0x4e2327)  
    #7 0x4d0863  (/home/muellein/git/jitq/backend/build/testbp+0x4d0863)  
    #8 0x4f78fe  (/home/muellein/git/jitq/backend/build/testbp+0x4f78fe)  
    #9 0x4f75c4  (/home/muellein/git/jitq/backend/build/testbp+0x4f75c4)  
    #10 0x4f74e0  (/home/muellein/git/jitq/backend/build/testbp+0x4f74e0)  
    #11 0x4f6ab0  (/home/muellein/git/jitq/backend/build/testbp+0x4f6ab0)  
    #12 0x4f6411  (/home/muellein/git/jitq/backend/build/testbp+0x4f6411)  
    #13 0x4f5259  (/home/muellein/git/jitq/backend/build/testbp+0x4f5259)  
    #14 0x4f501e  (/home/muellein/git/jitq/backend/build/testbp+0x4f501e)  
    #15 0x4eda97  (/home/muellein/git/jitq/backend/build/testbp+0x4eda97)  
    #16 0x4ed0c7  (/home/muellein/git/jitq/backend/build/testbp+0x4ed0c7)  
    #17 0x4ecdf2  (/home/muellein/git/jitq/backend/build/testbp+0x4ecdf2)  
    #18 0x4ecc12  (/home/muellein/git/jitq/backend/build/testbp+0x4ecc12)  
    #19 0x4ecaa3  (/home/muellein/git/jitq/backend/build/testbp+0x4ecaa3)  
    #20 0x4ec557  (/home/muellein/git/jitq/backend/build/testbp+0x4ec557)  
    #21 0x4ec44c  (/home/muellein/git/jitq/backend/build/testbp+0x4ec44c)  
    #22 0x4ec34a  (/home/muellein/git/jitq/backend/build/testbp+0x4ec34a)  
    #23 0x4eb13e  (/home/muellein/git/jitq/backend/build/testbp+0x4eb13e)  
    #24 0x4ea430  (/home/muellein/git/jitq/backend/build/testbp+0x4ea430)  
    #25 0x4e9f43  (/home/muellein/git/jitq/backend/build/testbp+0x4e9f43)  
    #26 0x4e9206  (/home/muellein/git/jitq/backend/build/testbp+0x4e9206)  
    #27 0x4e8af5  (/home/muellein/git/jitq/backend/build/testbp+0x4e8af5)  
    #28 0x4d06b7  (/home/muellein/git/jitq/backend/build/testbp+0x4d06b7)  
    #29 0x4cc025  (/home/muellein/git/jitq/backend/build/testbp+0x4cc025)  
  
SUMMARY: AddressSanitizer: heap-buffer-overflow (/home/muellein/git/jitq/backend/build/testbp+0x439151)  
Shadow bytes around the buggy address:  
  0x0c067fff7fb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c067fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c067fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c067fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c067fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x0c067fff8000: fa fa 00 00 00 05 fa fa 00 00[04]fa fa fa 00 00  
  0x0c067fff8010: 01 fa fa fa 00 00 04 fa fa fa 00 00 05 fa fa fa  
  0x0c067fff8020: 00 00 01 fa fa fa 00 00 00 00 fa fa 00 00 02 fa  
  0x0c067fff8030: fa fa 00 00 04 fa fa fa 00 00 00 03 fa fa 00 00  
  0x0c067fff8040: 04 fa fa fa 00 00 00 fa fa fa 00 00 03 fa fa fa  
  0x0c067fff8050: 00 00 00 07 fa fa fd fd fd fd fa fa fa fa fa fa  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07  
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
  Shadow gap:              cc  
==8012==ABORTING  
```  
  
Note that I have tested a few variable names of different lengths but they all cause a message that talks about a `20-byte region`. Maybe there is some statically-sized buffer somewhere?

---

## Comment 1

> Username: pepsiman  
> Created at: 2021-01-19 09:48:16 UTC  
> Updated at: 2021-01-19 09:54:49 UTC  
> Url: https://github.com/boostorg/process/issues/180#issuecomment-762728139  

Output with symbols:  
```  
=================================================================  
==462159==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x603000000053 at pc 0x7f4dd6e8ddfd bp 0x7ffdf268c940 sp 0x7ffdf268c0e8  
READ of size 29 at 0x603000000053 thread T0  
    #0 0x7f4dd6e8ddfc  (/lib/x86_64-linux-gnu/libasan.so.5+0xdadfc)  
    #1 0x55a9c20a7310 in bool std::__equal<true>::equal<char>(char const*, char const*, char const*) /usr/include/c++/9/bits/stl_algobase.h:834  
    #2 0x55a9c20a52fc in bool std::__equal_aux<char*, char*>(char*, char*, char*) /usr/include/c++/9/bits/stl_algobase.h:851  
    #3 0x55a9c20a189f in bool std::equal<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, char*>(__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, __gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, char*) /usr/include/c++/9/bits/stl_algobase.h:1069  
    #4 0x55a9c209e4d8 in boost::process::basic_environment_impl<char, boost::process::detail::posix::basic_environment_impl>::find(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) /usr/include/boost/process/environment.hpp:266  
    #5 0x55a9c209ac16 in boost::process::basic_environment_impl<char, boost::process::detail::posix::basic_environment_impl>::operator[](std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) /usr/include/boost/process/environment.hpp:355  
    #6 0x55a9c2097642 in boost::process::detail::env_builder<char>::operator()(boost::process::detail::env_set<char>&) /usr/include/boost/process/env.hpp:318  
    #7 0x55a9c2093f46 in void boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > >::operator()<boost::process::detail::env_set<char>&>(boost::process::detail::env_set<char>&) const /usr/include/boost/process/detail/execute_impl.hpp:149  
    #8 0x55a9c208f506 in void boost::fusion::detail::for_each_linear<boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 1>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > > >(boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 1>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > > const&, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > > const&, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > >&, mpl_::bool_<false>) /usr/include/boost/fusion/algorithm/iteration/detail/for_each.hpp:35  
    #9 0x55a9c208b603 in void boost::fusion::detail::for_each_linear<boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 0>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > > >(boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 0>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > > const&, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::fusion::vector_iterator<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, 4>, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > > const&, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > >&, mpl_::bool_<false>) /usr/include/boost/fusion/algorithm/iteration/detail/for_each.hpp:36  
    #10 0x55a9c2085eef in void boost::fusion::detail::for_each_dispatch<boost::fusion::filter_view<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > >, boost::fusion::forward_traversal_tag>(boost::fusion::filter_view<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >&, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > >&, boost::fusion::forward_traversal_tag) /usr/include/boost/fusion/algorithm/iteration/detail/for_each.hpp:46  
    #11 0x55a9c20825b3 in void boost::fusion::detail::for_each<boost::fusion::filter_view<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > > >(boost::fusion::filter_view<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >&, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > >&, mpl_::bool_<false>) /usr/include/boost/fusion/algorithm/iteration/detail/for_each.hpp:143  
    #12 0x55a9c207ff76 in boost::enable_if<boost::fusion::traits::is_sequence<boost::fusion::filter_view<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > > >, void>::type boost::fusion::for_each<boost::fusion::filter_view<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > > >(boost::fusion::filter_view<boost::fusion::tuple<char const (&) [13], boost::process::detail::env_set<char>&, boost::process::detail::env_append<char>&, boost::process::detail::posix::sig_init_&> const, boost::mpl::not_<boost::process::detail::is_initializer<mpl_::arg<-1> > > >&, boost::process::detail::builder_ref<boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> > >) /usr/include/boost/fusion/algorithm/iteration/for_each.hpp:35  
    #13 0x55a9c207d58c in boost::process::child boost::process::detail::basic_execute_impl<char, char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char>, boost::process::detail::posix::sig_init_>(char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&, boost::process::detail::posix::sig_init_&&) /usr/include/boost/process/detail/execute_impl.hpp:260  
    #14 0x55a9c207aa57 in boost::process::child boost::process::detail::execute_impl<char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char>, boost::process::detail::posix::sig_init_>(char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&, boost::process::detail::posix::sig_init_&&) /usr/include/boost/process/detail/execute_impl.hpp:278  
    #15 0x55a9c20779f8 in boost::process::child::child<char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char>, boost::process::detail::posix::sig_init_>(char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&, boost::process::detail::posix::sig_init_&&) /usr/include/boost/process/child.hpp:35  
    #16 0x55a9c2073d4a in int boost::process::detail::system_impl<boost::asio::io_context, char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char> >(std::integral_constant<bool, false>, std::integral_constant<bool, false>, char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&) /usr/include/boost/process/system.hpp:114  
    #17 0x55a9c2070242 in int boost::process::system<char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char> >(char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&) /usr/include/boost/process/system.hpp:149  
    #18 0x55a9c20615dc in main /home/mparsons/test.cpp:6  
    #19 0x7f4dd69c90b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)  
    #20 0x55a9c20610ed in _start (/home/mparsons/test+0x90ed)  
  
0x603000000053 is located 0 bytes to the right of 19-byte region [0x603000000040,0x603000000053)  
allocated by thread T0 here:  
    #0 0x7f4dd6ec2947 in operator new(unsigned long) (/lib/x86_64-linux-gnu/libasan.so.5+0x10f947)  
    #1 0x55a9c2077c7d in void std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_construct<char const*>(char const*, char const*, std::forward_iterator_tag) /usr/include/c++/9/bits/basic_string.tcc:219  
    #2 0x55a9c2073f1b in void std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_construct_aux<char const*>(char const*, char const*, std::__false_type) /usr/include/c++/9/bits/basic_string.h:247  
    #3 0x55a9c2070350 in void std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::_M_construct<char const*>(char const*, char const*) /usr/include/c++/9/bits/basic_string.h:266  
    #4 0x55a9c206d0ba in std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::basic_string<std::allocator<char> >(char const*, std::allocator<char> const&) /usr/include/c++/9/bits/basic_string.h:527  
    #5 0x55a9c20a14b1 in void std::_Construct<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, char*&>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*, char*&) /usr/include/c++/9/bits/stl_construct.h:75  
    #6 0x55a9c209de42 in std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >* std::__uninitialized_copy<false>::__uninit_copy<char**, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*>(char**, char**, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*) /usr/include/c++/9/bits/stl_uninitialized.h:83  
    #7 0x55a9c209a5c3 in std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >* std::uninitialized_copy<char**, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*>(char**, char**, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*) /usr/include/c++/9/bits/stl_uninitialized.h:140  
    #8 0x55a9c209717c in std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >* std::__uninitialized_copy_a<char**, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >(char**, char**, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >&) /usr/include/c++/9/bits/stl_uninitialized.h:307  
    #9 0x55a9c2097068 in std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >* std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::_M_allocate_and_copy<char**>(unsigned long, char**, char**) /usr/include/c++/9/bits/stl_vector.h:1511  
    #10 0x55a9c209356b in void std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::_M_assign_aux<char**>(char**, char**, std::forward_iterator_tag) /usr/include/c++/9/bits/vector.tcc:309  
    #11 0x55a9c208eff6 in void std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::_M_assign_dispatch<char**>(char**, char**, std::__false_type) /usr/include/c++/9/bits/stl_vector.h:1625  
    #12 0x55a9c208b456 in void std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::assign<char**, void>(char**, char**) /usr/include/c++/9/bits/stl_vector.h:766  
    #13 0x55a9c2085d44 in boost::process::detail::posix::basic_environment_impl<char>::basic_environment_impl(boost::process::detail::posix::native_environment_impl<char> const&) /usr/include/boost/process/detail/posix/environment.hpp:224  
    #14 0x55a9c208242a in boost::process::basic_environment_impl<char, boost::process::detail::posix::basic_environment_impl>::basic_environment_impl(boost::process::detail::posix::native_environment_impl<char> const&) /usr/include/boost/process/environment.hpp:314  
    #15 0x55a9c2082454 in boost::process::basic_environment<char>::basic_environment_impl(boost::process::detail::posix::native_environment_impl<char> const&) /usr/include/boost/process/environment.hpp:629  
    #16 0x55a9c2082535 in boost::process::detail::env_builder<char>::env_builder() /usr/include/boost/process/env.hpp:305  
    #17 0x55a9c207fe69 in boost::fusion::vector_detail::store<1ul, boost::process::detail::env_builder<char> >::store() /usr/include/boost/fusion/container/vector/vector.hpp:131  
    #18 0x55a9c207cec6 in boost::fusion::vector_detail::vector_data<std::integer_sequence<unsigned long, 0ul, 1ul>, boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> >::vector_data() /usr/include/boost/fusion/container/vector/vector.hpp:196  
    #19 0x55a9c207cf3b in boost::fusion::vector<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> >::vector() /usr/include/boost/fusion/container/vector/vector.hpp:274  
    #20 0x55a9c207cff9 in boost::fusion::set<boost::process::detail::exe_builder<char>, boost::process::detail::env_builder<char> >::set() /usr/include/boost/fusion/container/set/set.hpp:102  
    #21 0x55a9c207d531 in boost::process::child boost::process::detail::basic_execute_impl<char, char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char>, boost::process::detail::posix::sig_init_>(char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&, boost::process::detail::posix::sig_init_&&) /usr/include/boost/process/detail/execute_impl.hpp:257  
    #22 0x55a9c207aa57 in boost::process::child boost::process::detail::execute_impl<char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char>, boost::process::detail::posix::sig_init_>(char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&, boost::process::detail::posix::sig_init_&&) /usr/include/boost/process/detail/execute_impl.hpp:278  
    #23 0x55a9c20779f8 in boost::process::child::child<char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char>, boost::process::detail::posix::sig_init_>(char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&, boost::process::detail::posix::sig_init_&&) /usr/include/boost/process/child.hpp:35  
    #24 0x55a9c2073d4a in int boost::process::detail::system_impl<boost::asio::io_context, char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char> >(std::integral_constant<bool, false>, std::integral_constant<bool, false>, char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&) /usr/include/boost/process/system.hpp:114  
    #25 0x55a9c2070242 in int boost::process::system<char const (&) [13], boost::process::detail::env_set<char>, boost::process::detail::env_append<char> >(char const (&) [13], boost::process::detail::env_set<char>&&, boost::process::detail::env_append<char>&&) /usr/include/boost/process/system.hpp:149  
    #26 0x55a9c20615dc in main /home/mparsons/test.cpp:6  
    #27 0x7f4dd69c90b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)  
  
SUMMARY: AddressSanitizer: heap-buffer-overflow (/lib/x86_64-linux-gnu/libasan.so.5+0xdadfc)   
Shadow bytes around the buggy address:  
  0x0c067fff7fb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c067fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c067fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c067fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c067fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x0c067fff8000: fa fa 00 00 00 05 fa fa 00 00[03]fa fa fa 00 00  
  0x0c067fff8010: 04 fa fa fa 00 00 07 fa fa fa 00 00 02 fa fa fa  
  0x0c067fff8020: 00 00 00 00 fa fa 00 00 04 fa fa fa 00 00 07 fa  
  0x0c067fff8030: fa fa 00 00 03 fa fa fa 00 00 00 04 fa fa 00 00  
  0x0c067fff8040: 03 fa fa fa 00 00 01 fa fa fa 00 00 00 03 fa fa  
  0x0c067fff8050: 00 00 05 fa fa fa 00 00 04 fa fa fa 00 00 02 fa  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
  Shadow gap:              cc  
==462159==ABORTING  
```

---

## Comment 2

> Username: pepsiman  
> Created at: 2021-01-19 09:53:02 UTC  
> Url: https://github.com/boostorg/process/issues/180#issuecomment-762731158  

Duplicate of #122
