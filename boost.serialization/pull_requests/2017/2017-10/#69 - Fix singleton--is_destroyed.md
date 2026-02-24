# #69 Fix singleton::is_destroyed [Closed]

> Username: masterleinad  
> Created at: 2017-10-18 18:52:46 UTC  
> Updated at: 2018-04-27 11:51:46 UTC  
> Closed at: 2017-11-22 18:08:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/69  

This partially reverses commit b0a794da38b52c8ae0f8155cf3bb30321cf67f.  
Apparently, the logic related to  
```  
static bool & get_is_destroyed()  
{  
  static bool is_destroyed;  
  return is_destroyed;  
}  
BOOST_DLLEXPORT ~singleton()   
{  
  get_is_destroyed() = true;  
}  
```  
causes problem with gcc. In particular, a nested `type` might get deleted multiple times causing a segmentation fault.  
  
Reverting back to the implementation in which this piece of code is outsourced in a `detail` namespace, solves the issues in dealii/dealii#5262 and the ones reported in https://svn.boost.org/trac10/ticket/13186.

---

## Comment 1

> Username: vasild  
> Created_at: 2017-10-25 12:32:52 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-339314628  

I hit the same problem:  
  
```  
Invalid read of size 8  
   at 0x7A47B58: std::__1::__tree_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long> std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::__lower_bound<boost::serialization::typeid_system::extended_type_info_typeid_0 const*>(boost::serialization::typeid_system::extended_type_info_typeid_0 const* const&, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, std::__1::__tree_end_node<std::__1::__tree_node_base<void*>*>*) (__tree:2483)  
   by 0x7A472BA: lower_bound<const boost::serialization::typeid_system::extended_type_info_typeid_0 *> (__tree:1372)  
   by 0x7A472BA: lower_bound (set:1085)  
   by 0x7A472BA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:101)  
   by 0x81B8A4: ??? (in ...)  
   by 0x81B494: ??? (in ...)  
   by 0x9D62600: __cxa_finalize (in /lib/libc.so.7)  
   by 0x9CF1BE0: exit (in /lib/libc.so.7)  
   by 0x5E5185: ??? (in ...)  
   by 0x4023FFF: ???  
 Address 0xbc1c7f0 is 32 bytes inside a block of size 40 free'd  
   at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
   by 0x9D62600: __cxa_finalize (in /lib/libc.so.7)  
   by 0x7A3B833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
   by 0x7A61810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
   by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
   by 0x9D62604: __cxa_finalize (in /lib/libc.so.7)  
   by 0x9CF1BE0: exit (in /lib/libc.so.7)  
```  
  
Alas, this patch does not actually fix it. When applied, an assertion is hit:  
  
```  
Assertion failed: (! singleton<tkmap>::is_destroyed()), function type_unregister, file libs/serialization/src/extended_type_info_typeid.cpp, line 98.  
  
Program received signal SIGABRT, Aborted.  
0x0000000805ac305a in thr_kill () from /lib/libc.so.7  
(gdb) bt  
#0  0x0000000805ac305a in thr_kill () from /lib/libc.so.7  
#1  0x0000000805ac3024 in __raise (s=6) at /usr/src/lib/libc/gen/raise.c:52  
#2  0x0000000805ac2f99 in abort () at /usr/src/lib/libc/stdlib/abort.c:65  
#3  0x0000000805b40451 in __assert (func=<optimized out>, file=<optimized out>,   
    line=<optimized out>, failedexpr=<optimized out>) at /usr/src/lib/libc/gen/assert.c:51  
#4  0x000000080392b41d in boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister (  
    this=0x895ce8 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<crypto::hash> >::get_instance()::t>) at libs/serialization/src/extended_type_info_typeid.cpp:98  
#5  0x000000000081b8a5 in boost::serialization::extended_type_info_typeid<crypto::hash>::~extended_type_info_typeid (  
    this=0x895ce8 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<crypto::hash> >::get_instance()::t>)  
    at /usr/local/include/boost/serialization/extended_type_info_typeid.hpp:96  
#6  0x000000000081b495 in boost::serialization::singleton<boost::serialization::extended_type_info_typeid<crypto::hash> >::get_instance()::singleton_wrapper::~singleton_wrapper() (  
    this=0x895ce8 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<crypto::hash> >::get_instance()::t>) at /usr/local/include/boost/serialization/singleton.hpp:117  
#7  0x0000000805b33601 in __cxa_finalize (dso=0x0) at /usr/src/lib/libc/stdlib/atexit.c:235  
#8  0x0000000805ac2be1 in exit (status=1) at /usr/src/lib/libc/stdlib/exit.c:72  
#9  0x00000000005e5186 in _start ()  
  
```

---

## Comment 2

> Username: masterleinad  
> Created_at: 2017-10-25 13:03:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-339322542  

@vasild  Do you have a suitably small testcase?

---

## Comment 3

> Username: vasild  
> Created_at: 2017-10-25 13:17:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-339326325  

@masterleinad No :( I just built https://github.com/monero-project/monero/releases/tag/v0.11.1.0 and ran `monero-wallet-cli --help` - it is 100% deterministic and easy to reproduce, but not a small test case.

---

## Comment 4

> Username: vasild  
> Created_at: 2017-10-26 17:58:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-339748135  

There you go:  
  
```  
$ grep 'define BOOST_LIB_VERSION' /usr/local/include/boost/version.hpp  
#define BOOST_LIB_VERSION "1_65_1"  
  
$ cat a.cc  
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <sstream>  
#include <vector>  
  
void f()  
{  
  std::stringstream iss;  
  boost::archive::binary_iarchive ar(iss);  
  std::vector<int> out;  
  ar >> out;  
}  
  
int main(int argc, char* argv[])  
{  
  return 0;  
}  
  
$ c++ --version  
FreeBSD clang version 5.0.0 (tags/RELEASE_500/final 312559) (based on LLVM 5.0.0svn)  
Target: x86_64-unknown-freebsd11.1  
Thread model: posix  
InstalledDir: /usr/bin  
  
$ c++ -g a.cc -o a -I/usr/local/include -L/usr/local/lib -lboost_serialization  
  
(gdb) r  
Starting program: /tmp/a  
  
Program received signal SIGBUS, Bus error.  
boost::serialization::typeid_system::type_compare::operator() (  
    this=0x800a96c48 <boost::serialization::singleton<std::__1::multiset<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> > >::get_instance()::t+16>, lhs=0x5a5a5a5a5a5a5a5a,  
    rhs=0x611818 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at libs/serialization/src/extended_type_info_typeid.cpp:41  
41	        return lhs->is_less_than(*rhs);  
(gdb) bt  
#0  boost::serialization::typeid_system::type_compare::operator() (  
    this=0x800a96c48 <boost::serialization::singleton<std::__1::multiset<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> > >::get_instance()::t+16>, lhs=0x5a5a5a5a5a5a5a5a,  
    rhs=0x611818 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at libs/serialization/src/extended_type_info_typeid.cpp:41  
#1  0x0000000800854b67 in std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::__lower_bound<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> (this=<optimized out>,  
    __v=@0x7fffffffe110: 0x611818 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>,  
    __root=0x801c1c000,  
    __result=0x800a96c40 <boost::serialization::singleton<std::__1::multiset<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> > >::get_instance()::t+8>) at /usr/include/c++/v1/__tree:2483  
#2  0x00000008008542bb in std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::lower_bound<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> (  
    this=0x800a96c38 <boost::serialization::singleton<std::__1::multiset<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> > >::get_instance()::t>,  
    __v=@0x611818: 0x40d0d8 <vtable for boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >+16>) at /usr/include/c++/v1/__tree:1372  
#3  std::__1::multiset<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::lower_bound (  
    this=0x800a96c38 <boost::serialization::singleton<std::__1::multiset<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> > >::get_instance()::t>,  
    __k=@0x611818: 0x40d0d8 <vtable for boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >+16>) at /usr/include/c++/v1/set:1085  
#4  boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister (  
    this=0x611818 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at libs/serialization/src/extended_type_info_typeid.cpp:101  
#5  0x000000000040bdee in boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid (  
    this=0x611818 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at /usr/local/include/boost/serialization/extended_type_info_typeid.hpp:96  
#6  0x000000000040b8e5 in boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (  
    this=0x611818 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at /usr/local/include/boost/serialization/singleton.hpp:117  
#7  0x00000008014f7601 in __cxa_finalize (dso=0x0) at /usr/src/lib/libc/stdlib/atexit.c:235  
#8  0x0000000801486be1 in exit (status=0) at /usr/src/lib/libc/stdlib/exit.c:72  
#9  0x0000000000403bc6 in _start ()  
(gdb)  
  
$ valgrind ./a  
==46847== Memcheck, a memory error detector  
==46847== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.  
==46847== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info  
==46847== Command: ./a  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E50B58: std::__1::__tree_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long> std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::__lower_bound<boost::serialization::typeid_system::extended_type_info_typeid_0 const*>(boost::serialization::typeid_system::extended_type_info_typeid_0 const* const&, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, std::__1::__tree_end_node<std::__1::__tree_node_base<void*>*>*) (__tree:2483)  
==46847==    by 0x4E502BA: lower_bound<const boost::serialization::typeid_system::extended_type_info_typeid_0 *> (__tree:1372)  
==46847==    by 0x4E502BA: lower_bound (set:1085)  
==46847==    by 0x4E502BA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:101)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200580 is 32 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E50B75: std::__1::__tree_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long> std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::__lower_bound<boost::serialization::typeid_system::extended_type_info_typeid_0 const*>(boost::serialization::typeid_system::extended_type_info_typeid_0 const* const&, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, std::__1::__tree_end_node<std::__1::__tree_node_base<void*>*>*) (__tree:2483)  
==46847==    by 0x4E502BA: lower_bound<const boost::serialization::typeid_system::extended_type_info_typeid_0 *> (__tree:1372)  
==46847==    by 0x4E502BA: lower_bound (set:1085)  
==46847==    by 0x4E502BA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:101)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200560 is 0 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E50BBB: std::__1::__tree_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long> std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::__upper_bound<boost::serialization::typeid_system::extended_type_info_typeid_0 const*>(boost::serialization::typeid_system::extended_type_info_typeid_0 const* const&, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, std::__1::__tree_end_node<std::__1::__tree_node_base<void*>*>*) (__tree:2523)  
==46847==    by 0x4E502E6: upper_bound<const boost::serialization::typeid_system::extended_type_info_typeid_0 *> (__tree:1388)  
==46847==    by 0x4E502E6: upper_bound (set:1103)  
==46847==    by 0x4E502E6: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:102)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200580 is 32 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E50BD5: std::__1::__tree_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long> std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::__upper_bound<boost::serialization::typeid_system::extended_type_info_typeid_0 const*>(boost::serialization::typeid_system::extended_type_info_typeid_0 const* const&, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, std::__1::__tree_end_node<std::__1::__tree_node_base<void*>*>*) (__tree:2523)  
==46847==    by 0x4E502E6: upper_bound<const boost::serialization::typeid_system::extended_type_info_typeid_0 *> (__tree:1388)  
==46847==    by 0x4E502E6: upper_bound (set:1103)  
==46847==    by 0x4E502E6: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:102)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200568 is 8 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E50300: __tree_next_iter<std::__1::__tree_end_node<std::__1::__tree_node_base<void *> *> *, std::__1::__tree_node_base<void *> *> (__tree:185)  
==46847==    by 0x4E50300: operator++ (__tree:921)  
==46847==    by 0x4E50300: operator++ (__tree:927)  
==46847==    by 0x4E50300: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200568 is 8 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E50304: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:107)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200580 is 32 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E5037A: __tree_is_left_child<std::__1::__tree_node_base<void *> *> (__tree:84)  
==46847==    by 0x4E5037A: __tree_next_iter<std::__1::__tree_end_node<std::__1::__tree_node_base<void *> *> *, std::__1::__tree_node_base<void *> *> (__tree:187)  
==46847==    by 0x4E5037A: operator++ (__tree:921)  
==46847==    by 0x4E5037A: operator++ (__tree:927)  
==46847==    by 0x4E5037A: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200570 is 16 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E50C00: __tree_next_iter<std::__1::__tree_end_node<std::__1::__tree_node_base<void *> *> *, std::__1::__tree_node_base<void *> *> (__tree:185)  
==46847==    by 0x4E50C00: operator++ (__tree:840)  
==46847==    by 0x4E50C00: std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::erase(std::__1::__tree_const_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long>) (__tree:2361)  
==46847==    by 0x4E503AA: erase (set:1036)  
==46847==    by 0x4E503AA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200568 is 8 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E50C1D: __tree_is_left_child<std::__1::__tree_node_base<void *> *> (__tree:84)  
==46847==    by 0x4E50C1D: __tree_next_iter<std::__1::__tree_end_node<std::__1::__tree_node_base<void *> *> *, std::__1::__tree_node_base<void *> *> (__tree:187)  
==46847==    by 0x4E50C1D: operator++ (__tree:840)  
==46847==    by 0x4E50C1D: std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::erase(std::__1::__tree_const_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long>) (__tree:2361)  
==46847==    by 0x4E503AA: erase (set:1036)  
==46847==    by 0x4E503AA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200570 is 16 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E492C0: void std::__1::__tree_remove<std::__1::__tree_node_base<void*>*>(std::__1::__tree_node_base<void*>*, std::__1::__tree_node_base<void*>*) (__tree:357)  
==46847==    by 0x4E50C72: std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::erase(std::__1::__tree_const_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long>) (__tree:2366)  
==46847==    by 0x4E503AA: erase (set:1036)  
==46847==    by 0x4E503AA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200560 is 0 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E492EF: void std::__1::__tree_remove<std::__1::__tree_node_base<void*>*>(std::__1::__tree_node_base<void*>*, std::__1::__tree_node_base<void*>*) (__tree:360)  
==46847==    by 0x4E50C72: std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::erase(std::__1::__tree_const_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long>) (__tree:2366)  
==46847==    by 0x4E503AA: erase (set:1036)  
==46847==    by 0x4E503AA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200568 is 8 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 8  
==46847==    at 0x4E492FF: __tree_is_left_child<std::__1::__tree_node_base<void *> *> (__tree:84)  
==46847==    by 0x4E492FF: void std::__1::__tree_remove<std::__1::__tree_node_base<void*>*>(std::__1::__tree_node_base<void*>*, std::__1::__tree_node_base<void*>*) (__tree:366)  
==46847==    by 0x4E50C72: std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::erase(std::__1::__tree_const_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long>) (__tree:2366)  
==46847==    by 0x4E503AA: erase (set:1036)  
==46847==    by 0x4E503AA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200570 is 16 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid read of size 1  
==46847==    at 0x4E494C5: void std::__1::__tree_remove<std::__1::__tree_node_base<void*>*>(std::__1::__tree_node_base<void*>*, std::__1::__tree_node_base<void*>*) (__tree:380)  
==46847==    by 0x4E50C72: std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::erase(std::__1::__tree_const_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long>) (__tree:2366)  
==46847==    by 0x4E503AA: erase (set:1036)  
==46847==    by 0x4E503AA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200578 is 24 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847== Invalid free() / delete / delete[] / realloc()  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x4E50C7A: __libcpp_deallocate (new:234)  
==46847==    by 0x4E50C7A: deallocate (memory:1750)  
==46847==    by 0x4E50C7A: deallocate (memory:1510)  
==46847==    by 0x4E50C7A: std::__1::__tree<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::__1::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> >::erase(std::__1::__tree_const_iterator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, std::__1::__tree_node<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, void*>*, long>) (__tree:2370)  
==46847==    by 0x4E503AA: erase (set:1036)  
==46847==    by 0x4E503AA: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (extended_type_info_typeid.cpp:108)  
==46847==    by 0x40BDED: boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==46847==    by 0x40B8E4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in /tmp/a)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==  Address 0x6200560 is 0 bytes inside a block of size 40 free'd  
==46847==    at 0x4C268ED: free (in /usr/local/lib/valgrind/vgpreload_memcheck-amd64-freebsd.so)  
==46847==    by 0x5AF3600: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x4E44833: __do_global_dtors_aux (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x4E6A810: ??? (in /usr/ports/devel/boost-libs/work/stage/usr/local/lib/libboost_serialization.so.1.65.1)  
==46847==    by 0x400585A: ??? (in /libexec/ld-elf.so.1)  
==46847==    by 0x5AF3604: __cxa_finalize (in /lib/libc.so.7)  
==46847==    by 0x5A82BE0: exit (in /lib/libc.so.7)  
==46847==    by 0x403BC5: (below main) (in /tmp/a)  
==46847==   
==46847==   
==46847== HEAP SUMMARY:  
==46847==     in use at exit: 1,176 bytes in 2 blocks  
==46847==   total heap usage: 3 allocs, 2 frees, 1,216 bytes allocated  
==46847==   
==46847== LEAK SUMMARY:  
==46847==    definitely lost: 0 bytes in 0 blocks  
==46847==    indirectly lost: 0 bytes in 0 blocks  
==46847==      possibly lost: 0 bytes in 0 blocks  
==46847==    still reachable: 1,176 bytes in 2 blocks  
==46847==         suppressed: 0 bytes in 0 blocks  
==46847== Rerun with --leak-check=full to see details of leaked memory  
==46847==   
==46847== For counts of detected and suppressed errors, rerun with: -v  
==46847== ERROR SUMMARY: 14 errors from 14 contexts (suppressed: 0 from 0)  
```

---

## Comment 5

> Username: masterleinad  
> Created_at: 2017-10-26 21:32:54 UTC  
> Updated_at: 2017-10-26 21:33:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-339807398  

@vasild In the code above you are trying to load from an empty archive and this is causing the error. If you fill the archive before as follows everything should be fine:  
```  
#include <boost/archive/binary_oarchive.hpp>  
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <sstream>  
#include <vector>  
   
int main()  
{  
  std::stringstream iss;  
  {  
    boost::archive::binary_oarchive ar(iss);  
    std::vector<int> in;  
    ar & in;  
  }  
   
  {  
    boost::archive::binary_iarchive ar(iss);  
    std::vector<int> out;  
    ar & out;  
  }  
  return 0;  
}  
```

---

## Comment 6

> Username: vasild  
> Created_at: 2017-10-27 05:45:08 UTC  
> Updated_at: 2017-10-27 05:45:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-339878478  

@masterleinad   
  
* In the program I posted, the code that fiddles with `boost::archive` and `std::vector` is not even executed. Notice that the function f() is not called at all.  
  
* The crash occurs during `exit()`.  
  
* Your program above (the one that you posted) produces the same crash during `exit()`:  
```  
$ cat a.cc   
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/archive/binary_oarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <sstream>  
#include <vector>  
  
int main(int argc, char* argv[])  
{  
  std::stringstream iss;  
  {  
    boost::archive::binary_oarchive ar(iss);  
    std::vector<int> in;  
    ar & in;  
  }  
  
  {  
    boost::archive::binary_iarchive ar(iss);  
    std::vector<int> out;  
    ar & out;  
  }  
}  
  
$ c++ -g a.cc -o a -I/usr/local/include -L/usr/local/lib -lboost_serialization  
  
(gdb) r  
Starting program: /tmp/a   
  
Program received signal SIGBUS, Bus error.  
0x0000000800854ab4 in boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() () from /usr/local/lib/libboost_serialization.so.1.65.1  
(gdb) bt  
#0  0x0000000800854ab4 in boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() () from /usr/local/lib/libboost_serialization.so.1.65.1  
#1  0x000000000040cdde in boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid (  
    this=0x617a88 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at /usr/local/include/boost/serialization/extended_type_info_typeid.hpp:96  
#2  0x000000000040c8d5 in boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (  
    this=0x617a88 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at /usr/local/include/boost/serialization/singleton.hpp:117  
#3  0x00000008014d7601 in __cxa_finalize (dso=0x0) at /usr/src/lib/libc/stdlib/atexit.c:235  
#4  0x0000000801466be1 in exit (status=0) at /usr/src/lib/libc/stdlib/exit.c:72  
#5  0x0000000000404ca6 in _start ()  
(gdb)   
```  
  
All of the above is with unmodified Boost 1.65.1. When the patch from this pull request is applied, both programs produce the same result during `exit()`:  
```  
Assertion failed: (! singleton<tkmap>::is_destroyed()), function type_unregister, file libs/serialization/src/extended_type_info_typeid.cpp, line 98.  
  
Program received signal SIGABRT, Aborted.  
0x000000080148d05a in thr_kill () from /lib/libc.so.7  
(gdb) bt  
#0  0x000000080148d05a in thr_kill () from /lib/libc.so.7  
#1  0x000000080148d024 in __raise (s=6) at /usr/src/lib/libc/gen/raise.c:52  
#2  0x000000080148cf99 in abort () at /usr/src/lib/libc/stdlib/abort.c:65  
#3  0x000000080150a451 in __assert (func=<optimized out>, file=<optimized out>,   
    line=<optimized out>, failedexpr=<optimized out>) at /usr/src/lib/libc/gen/assert.c:51  
#4  0x000000080085a41d in boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister (  
    this=0x617b58 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at libs/serialization/src/extended_type_info_typeid.cpp:98  
#5  0x000000000040ceae in boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > >::~extended_type_info_typeid (  
    this=0x617b58 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at /usr/local/include/boost/serialization/extended_type_info_typeid.hpp:96  
#6  0x000000000040c9a5 in boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (  
    this=0x617b58 <boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::__1::vector<int, std::__1::allocator<int> > > >::get_instance()::t>)  
    at /usr/local/include/boost/serialization/singleton.hpp:117  
#7  0x00000008014fd601 in __cxa_finalize (dso=0x0) at /usr/src/lib/libc/stdlib/atexit.c:235  
#8  0x000000080148cbe1 in exit (status=0) at /usr/src/lib/libc/stdlib/exit.c:72  
#9  0x0000000000404d76 in _start ()  
```

---

## Comment 7

> Username: vasild  
> Created_at: 2017-10-28 09:22:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-340164619  

@masterleinad @robertramey   
  
I dug a little bit deeper into this.  
  
Test program  
```  
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <sstream>  
#include <vector>  
  
void f()  
{  
  std::stringstream iss;  
  boost::archive::binary_iarchive ar(iss);  
  std::vector<int> out;  
  ar >> out;  
}  
  
int main(int argc, char* argv[])  
{  
  return 0;  
}  
```  
Notice that function `f()` is not called at all.  
  
Crashes as mentioned above with clang 5.0.0, clang 6.0.0 and gcc 7.2.1. Not compiler or OS dependent, although I have this option set to `true` in my malloc config:  
```  
       "opt.junk" (const char *) r- [--enable-fill]  
           Junk filling. If set to "alloc", each byte of uninitialized  
           allocated memory will be initialized to 0xa5. If set to "free", all  
           deallocated memory will be initialized to 0x5a. If set to "true",  
           both allocated and deallocated memory will be initialized, and if  
           set to "false", junk filling be disabled entirely. This is intended  
           for debugging and will impact performance negatively. This option  
           is "false" by default unless --enable-debug is specified during  
           configuration, in which case it is "true" by default unless running  
           inside Valgrind[2].  
```  
Without that the bug may remain unnoticed because using a memory that has just been `free()`d and not yet reused will read the "expected" contents. Valgrind always reports the read-after-use, of course.  
  
I confirmed that the bug was introduced in b0a794da. There is no crash with `b0a794da~` and a crash with b0a794da (which is from Feb 2017).  
  
For some reason the patch in this pull request produces an assertion failure when applied to the released Boost 1.65.1 (see above), but when applied to the latest code from git daf20da0 it fixes the bug (no crash or assert).  
  
So, I guess that if @robertramey confirms it is ok to revert the main part of b0a794da, then the patch in thus pull request should be pushed and included in the next release.

---

## Comment 8

> Username: robertramey  
> Created_at: 2017-10-31 22:59:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-340931923  

I'm interested in this but don't really understand what's being asked for.  
  
> So, I guess that if @robertramey confirms it is ok to revert the main part of b0a794d, then the patch in thus pull request should be pushed and included in the next release."

---

## Comment 9

> Username: vasild  
> Created_at: 2017-11-01 06:48:13 UTC  
> Updated_at: 2017-11-01 06:48:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341004503  

@robertramey Hello, shortly - the question to you is - do you think that the patch in this pull request is ok to be merged - i.e. do you approve it?  
  
Longer story:  
* The latest code contains a crashing bug (use-after-free, see above for a simple test program and how to reproduce it).  
* b0a794d introduced this bug.  
* This pull request proposes a fix, which reverts parts of b0a794d. As such it may break something that was fixed in b0a794d and so it is best to check with the author of b0a794d.  
  
To me it looks like b0a794d has three unrelated changes - one MinGW change in `appveyor.yml`, some simplification to `singleton.hpp` and the addition of some asserts. If the changes to `singleton.hpp` are indeed simplifications only and have nothing to do with MinGW, then I guess it would be ok to revert them with this pull request in order to get the bug fixed.

---

## Comment 10

> Username: davydden  
> Created_at: 2017-11-01 07:13:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341012465  

> see above for a simple test program and how to reproduce it  
  
it would probably be good to add this as a regression test in Boost::serialization.

---

## Comment 11

> Username: robertramey  
> Created_at: 2017-11-01 15:40:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341144554  

@davydden  
  
What does "this" refer to?  
  
The patch eliminates the changes intended to address issue related to "visibility" on various platforms.  Have you actually run the test test suite with this patch included?  With the shared build? On which platforms/compilers?

---

## Comment 12

> Username: davydden  
> Created_at: 2017-11-01 15:45:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341145872  

@robertramey   
  
> What does "this" refer to?  
  
I mean the test program by @vasild which shows the issue. So if the issue is fixed by this patch, I think it's good to have a regression test to make sure the same issue does not appear again later.  
  
> Have you actually run the test test suite  
  
I have not. I just assume that prior to release boost runs the testsuite on for target compilers/os/etc and since this issue is present in 1.65.1. then most likely there was not a failing test to indicate it.

---

## Comment 13

> Username: robertramey  
> Created_at: 2017-11-01 15:56:36 UTC  
> Updated_at: 2017-11-01 16:27:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341149505  

I ran the test and it passes under Xcode on my machine and it passes with no problem - Of course.  I'm running the static build with clang.    
  
This illustrates the problem with just merging in a PR.  The whole "visibility" facility is required for a library of this size, but it's implemented with different syntax and rules among the various compilers.   Fixing one usually breaks another so the whole thing turns into an endless game of whack-a-mole.  For now, I'll add this to the test suite and check it in so it shows up on the Boost test matrix.  Hopefully that will shed some light on the issue.  
  
Update: I just ran the test on all combinations of the following  link=static,shared variant=debug,release toolset=darwin-6.2~03,clang-03,darwin-6.2~11,clang-11,darwin-6.2~14,clang-14 with no failures.  That's 24 combinations.  
  
The test is at the last line labeled test_z.  Tests not run are labeled "missing"  
  
[library_status.html.zip](https://github.com/boostorg/serialization/files/1434831/library_status.html.zip)  
  
BTW there is currently a test in the boost test matrix labeled test_inclusion which pretty much does nothing and still fails.  I'm thinking it may be the same test test we're looking at here.  You might want to check that out see if it's the same.

---

## Comment 14

> Username: vasild  
> Created_at: 2017-11-01 16:38:05 UTC  
> Updated_at: 2017-11-01 16:44:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341162418  

@robertramey so you ran the test program from https://github.com/boostorg/serialization/pull/69#issuecomment-340164619 on unpatched Boost (without this PR) and got no crash? But did you instruct your allocator to wipe memory after `free()` or did you ran the test under Valgrind?

---

## Comment 15

> Username: robertramey  
> Created_at: 2017-11-01 16:48:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341165472  

>so you ran the test program from #69 (comment) and got no crash?  
  
yep  
  
> But did you instruct your allocator to wipe memory after free() or did you ran the test under Valgrind?  
  
Nope, I actually don't know how to do either of these neither from Xcode nor from the command line.  I did managed to enable the address sanitizer from Xcode and got a bunch of linker errors.  I would like to enable the sanitizes in general.  But I can't do that be cause the many of the tests for de-serialization of pointers have memory leaks so I get too many false positives.  Update the tests to eliminate the false positives would be a big job give the number of tests we have.

---

## Comment 16

> Username: vasild  
> Created_at: 2017-11-01 17:03:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341170254  

@robertramey Alright, then what you see is expected - the bug remains hidden because the code executed at program exit does something equivalent to:  
```  
char* p = malloc(1);  
*p = 'a';  
free(p);  
read *p and assume it is still 'a'  
```  
this may work in some cases and remain unnoticed.  
  
Here is how to tell Mac OS X's malloc to rewrite the memory after `free()`:  
from https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man3/malloc.3.html  
```  
     MallocScribble               ... if set, fill memory  
                                  that has been deallocated with 0x55 bytes.  This increases the likelihood  
                                  that a program will fail due to accessing memory that is no longer allocated.  
```  
So, I guess it would be something like  
```  
$ MallocScribble=1 ./test_program  
```  
About Valgrind, well that would be:  
```  
$ valgrind ./test_program  
```

---

## Comment 17

> Username: robertramey  
> Created_at: 2017-11-01 18:05:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341190069  

Per the instructions invoked:  
$export MallocScribble=1  
and re-ran the the 24 tests.  Here is what the output looks like.  Looks OK to me.   
  
```  
darwin.link ../../../bin.v2/libs/serialization/test/test_z.test/darwin-darwin-6.2~03/release/link-static/test_z  
sh(24068,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
g++(24072,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
collect2(24074,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
ld(24075,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
ld(24075,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
darwin.link ../../../bin.v2/libs/serialization/test/test_z.test/darwin-darwin-6.2~03/release/test_z  
sh(24077,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
g++(24078,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
collect2(24080,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
ld(24081,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
ld(24081,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_z.test/darwin-darwin-6.2~03/release/link-static/test_z.run  
sh(24082,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
cp(24085,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
**passed** ../../../bin.v2/libs/serialization/test/test_z.test/darwin-darwin-6.2~03/release/link-static/test_z.test  
sh(24086,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_z.test/darwin-darwin-6.2~03/release/test_z.run  
sh(24084,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
cp(24089,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
**passed** ../../../bin.v2/libs/serialization/test/test_z.test/darwin-darwin-6.2~03/release/test_z.test  
sh(24091,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/debug/test_z.o  
sh(24073,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24073,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24073,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang(24079,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/debug/link-static/test_z.o  
sh(24070,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24070,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24070,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang(24076,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang-darwin.link ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/debug/test_z  
sh(24094,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24094,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24094,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
ld(24095,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/debug/test_z.run  
sh(24096,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
cp(24100,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
**passed** ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/debug/test_z.test  
sh(24101,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang-darwin.link ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/debug/link-static/test_z  
sh(24097,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24097,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24097,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
ld(24099,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/debug/link-static/test_z.run  
sh(24102,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
cp(24104,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
**passed** ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/debug/link-static/test_z.test  
sh(24105,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/release/link-static/test_z.o  
sh(24088,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24088,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24088,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang(24090,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/release/test_z.o  
sh(24092,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24092,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24092,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang(24093,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang-darwin.link ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/release/link-static/test_z  
sh(24106,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24106,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24106,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
ld(24107,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/release/link-static/test_z.run  
sh(24108,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
cp(24111,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
**passed** ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/release/link-static/test_z.test  
sh(24112,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang-darwin.link ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/release/test_z  
sh(24109,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24109,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
clang++(24109,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
ld(24113,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/release/test_z.run  
sh(24114,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
cp(24116,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
**passed** ../../../bin.v2/libs/serialization/test/test_z.test/clang-darwin-darwin-03/release/test_z.test  
sh(24117,0x7fffe657f3c0) malloc: enabling scribbling to detect mods to free blocks  
...updated 32 targets...  
$  
```

---

## Comment 18

> Username: vasild  
> Created_at: 2017-11-01 18:35:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341198806  

@robertramey what about the test program I posted? Maybe the tests above are somewhat different?  
  
```  
$ cat a.cc  
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <sstream>  
#include <vector>  
  
void f()  
{  
  std::stringstream iss;  
  boost::archive::binary_iarchive ar(iss);  
  std::vector<int> out;  
  ar >> out;  
}  
  
int main(int argc, char* argv[])  
{  
  return 0;  
}  
  
$ c++ -g a.cc -o a -I/usr/local/include -L/usr/local/lib -lboost_serialization  
  
$ MallocScribble=1 ./a  
```

---

## Comment 19

> Username: robertramey  
> Created_at: 2017-11-01 18:42:47 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341200925  

it's the same program I ran, I copied from the post above.  I just named it test_z.cpp.    
  
Note that boost test matrix here   
  
http://www.boost.org/development/tests/develop/developer/serialization.html  
  
Has a test named test_inclusion which I believe is showing similar problems on some platforms.  I'm speculating that on some platforms some code doesn't get linked in properly or at all and this shows up as a linking error or as a runtime error when trying to run code from a shared library.  Is your combination/platform on the boost regression tests.  If so which one is it?  Is the library passing all tests there?

---

## Comment 20

> Username: vasild  
> Created_at: 2017-11-01 20:11:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341226613  

@robertramey   
Strange, so you cannot reproduce this problem. I did not expect this to be environment dependent as I reproduced it with clang 4, 5 and 6 and gcc 7. My OS is FreeBSD 11. In this matrix http://www.boost.org/development/tests/develop/developer/serialization.html `test_inclusion` is all green/pass on FreeBSD.  
  
I will try to reproduce this on Linux...

---

## Comment 21

> Username: robertramey  
> Created_at: 2017-11-02 03:21:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341306903  

The changes to singleton should only make a difference for a shared library implementation.  The test matrix doesn't indicate whether the shared or static library is being used.  So I don't know that the test matrix is helpful here.

---

## Comment 22

> Username: vasild  
> Created_at: 2017-11-02 09:09:12 UTC  
> Updated_at: 2017-11-02 09:11:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341359352  

I can't seem to be able to reproduce the crash with my test prog on Linux with gcc 7.2.0 or clang 6.0.0.  
  
Also, `serialization/test/test_inclusion.cpp` does not crash in the same environment where my test prog crashes (FreeBSD 11 + clang 5).  
  
So:  
* the problem this PR is targeting is somewhat OS/environment dependent and  
* my test prog is testing different codepaths than `serialization/test/test_inclusion.cpp`.  
  
@masterleinad Can you demonstrate the bug in your environment with:  
  
```  
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <sstream>  
#include <vector>  
  
void f()  
{  
  std::stringstream iss;  
  boost::archive::binary_iarchive ar(iss);  
  std::vector<int> out;  
  ar >> out;  
}  
  
int main(int argc, char* argv[])  
{  
  return 0;  
}  
```  
either under Valgrind, or by configuring malloc to wipe memory after `free()` or in some other way?  
  
PS yes, I am using shared libraries (libboost_serialization.so).

---

## Comment 23

> Username: masterleinad  
> Created_at: 2017-11-02 13:49:36 UTC  
> Updated_at: 2017-11-02 13:49:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341426411  

@vasild  No, I don't find any boost version for which this code would fail for me. Neither for `clang-5` nor `gcc-7.2.0` I see any problems.  
  
The original cases I wrote this PR for are rather complex. I try to come up with a minimal example...

---

## Comment 24

> Username: vasild  
> Created_at: 2017-11-02 13:53:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341427615  

@masterleinad I guess you tried with Valgrind?

---

## Comment 25

> Username: masterleinad  
> Created_at: 2017-11-02 14:03:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-341430722  

Yes, I tried with valgrind and `-fsanitize=leak`, `-fsanitize=memory` and `-fsanitize=address` and didn't notice anything.

---

## Comment 26

> Username: masterleinad  
> Created_at: 2017-11-22 18:08:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-346431062  

Closed in favor of #79.

---

## Comment 27

> Username: Flamefire  
> Created_at: 2018-04-25 15:54:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-384338115  

Although this is closed I wanted to say you were on the right track. The issue is the following:  
  
- `extended_type_info_typeid` is used which causes a singleton to be created  
- It registers in the `ktmap` singleton (https://github.com/boostorg/serialization/blob/b73329fe45b02965c9f2059f7d90c285db1b3630/src/extended_type_info_typeid.cpp#L91)  
- On exit the `ktmap` singleton gets destroyed before the `extended_type_info_typeid` singleton. However `~singleton` is never called, because no instance exists. Only `~singleton_wrapper` is called.  
-  `extended_type_info_typeid` singleton gets destroyed and access `ktmap` in it dtor (https://github.com/boostorg/serialization/blob/b73329fe45b02965c9f2059f7d90c285db1b3630/src/extended_type_info_typeid.cpp#L99)  
- as `destroyed` is not set, it accesses dead memory  
  
The problem is very complicated to reproduce, due to unknown init/finalize order. I assume: `typeid`-singleton gets created and registers for destruction. It calls into `ktmap` which causes its instantiation and registers `ktmap` for destruction. Finalize order is the reverse so `ktmap` is destroyed first.  
  
So one problem is the usage of the `singleton` ctor/dtor which makes people assume it is used.

---

## Comment 28

> Username: vasild  
> Created_at: 2018-04-25 16:39:49 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-384352900  

Wow, finally @Flamefire nailed this down! Thanks for the thorough analysis!

---

## Comment 29

> Username: Flamefire  
> Created_at: 2018-04-27 11:51:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/69#issuecomment-384947955  

@masterleinad What did you mean with  
  
> In particular, a nested type might get deleted multiple times causing a segmentation fault.  
  
Every instance should be deleted exactly once. Not more, not less. Do you have anything to check this?

---
