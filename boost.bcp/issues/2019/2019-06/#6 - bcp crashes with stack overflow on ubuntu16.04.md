# #6 - bcp crashes with stack overflow on ubuntu16.04 [Open]

> Username: firewave  
> Created at: 2019-06-18 17:13:39 UTC  
> Updated at: 2019-11-28 12:27:13 UTC  
> Url: https://github.com/boostorg/bcp/issues/6  

Using https://github.com/conan-community/conan-boost with `boost:namespace = boost_ns` and `boost:namespace_alias = True` will fail with Boost 1.70 on ubuntu 16.04 with a core dump.  
  
```  
...  
INFO: tracking source dependencies of library log due to presence of "class light_rw_mutex  
{" in file "boost/log/detail/light_rw_mutex.hpp"  
INFO: tracking source dependencies of library random due to presence of "class random_device : private noncopyable  
{" in file "boost/random/random_device.hpp"  
CAUTION: don't know how to trace depenencies through macro: "BOOST_SPIRIT_HEADER" in file: libs/spirit/repository/test/test_headers/test.cpp  
CAUTION: don't know how to trace depenencies through macro: "BOOST_SPIRIT_HEADER" in file: libs/spirit/repository/test/test_headers/test.cpp  
CAUTION: don't know how to trace depenencies through macro: "BOOST_SPIRIT_HEADER" in file: libs/spirit/test/test_headers/test.cpp  
CAUTION: don't know how to trace depenencies through macro: "BOOST_SPIRIT_HEADER" in file: libs/spirit/test/test_headers/test.cpp  
Segmentation fault (core dumped)  
```  
  
The stack trace looks like this and goes on with the same two function calls  
  
```  
#0  0x00007fbbf6ae2bbc in _int_malloc (av=av@entry=0x7fbbf6e25b20 <main_arena>, bytes=bytes@entry=64) at malloc.c:3353  
#1  0x00007fbbf6ae5184 in __GI___libc_malloc (bytes=64) at malloc.c:2913  
#2  0x00007fbbf70cee78 in operator new(unsigned long) () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#3  0x000000000041b931 in std::_Rb_tree<boost::filesystem::path, boost::filesystem::path, std::_Identity<boost::filesystem::path>, path_less, std::allocator<boost::filesystem::path> >::_M_insert_unique(boost::filesystem::path const&) ()  
#4  0x000000000041542f in bcp_implementation::add_file(boost::filesystem::path const&) ()  
#5  0x0000000000419dc6 in bcp_implementation::add_path(boost::filesystem::path const&) ()  
```  
  
The bcp command-line  
  
```  
bcp --namespace=boost_ns --namespace-alias --boost=boost_1_70_0 compatibility uuid array tti lockfree utility units archive signals2 preprocessor move program_options system config static_assert bind algorithm ratio crc regex property_tree process property_map yap ptr_container exception safe_numerics intrusive pool function_types timer coroutine icl boost-build.jam fusion lexical_cast build contract flyweight scope_exit typeof endian variant multi_index msm tuple mpi callable_traits statechart local_function numeric hof vmd fiber chrono polygon detail outcome core accumulators graph histogram coroutine2 multiprecision type_erasure poly_collection bimap context python geometry asio boost_install compute mp11 xpressive disjoint_sets spirit graph_parallel foreach logic test atomic type_traits date_time pending thread iostreams iterator qvm circular_buffer locale function log beast optional functional integer container concept assert dynamic_bitset unordered range io conversion type_index serialization any dll predef align concept_check assign phoenix stacktrace format convert hana parameter metaparse mpl proto heap multi_array lambda parameter_python math random filesystem rational sort boostcpp.jam headers gil container_hash throw_exception interprocess tokenizer winapi smart_ptr wave custom-boost  
```  
  
It worked fine with Boost 1.69. It also works with 1.70 on CentOS 7, ubuntu 18.04, ubuntu 19.04 and Kali 2019.2.  
  
I am using the default compiler on a x86_64 machine.

---

## Comment 1

> Username: firewave  
> Created at: 2019-07-11 10:34:41 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-510431039  

The does not occur when you build it in a Docker container which is running ubuntu 16.04, but the host system is *not* ubuntu 16.04. If both the container and the host run it the issue occurs.

---

## Comment 2

> Username: firewave  
> Created at: 2019-07-17 09:00:31 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-512167845  

On a different system the last log messages differed  
  
```  
CAUTION: dependent file libs/sort/doc/graph/windows_string_sort_files/editdata does not exist.  
   Found while scanning file libs/sort/doc/graph/windows_string_sort_files/chart001.htm  
CAUTION: dependent file libs/sort/doc/graph/windows_string_sort_files/stylesheet does not exist.  
   Found while scanning file libs/sort/doc/graph/windows_string_sort_files/chart001.htm  
CAUTION: dependent file libs/sort/doc/graph/windows_string_sort_files/image001 does not exist.  
   Found while scanning file libs/sort/doc/graph/windows_string_sort_files/chart001.htm  
Segmentation fault (core dumped)  
```

---

## Comment 3

> Username: steveire  
> Created at: 2019-10-31 14:30:16 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-548402879  

I'm seeing this too in a 16.04 docker hosted in 18.04

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-10-31 19:27:17 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-548532461  

I'm unable to reproduce on either MSVC or Ubunutu-19.10, so I will need more information.  
  
Infinite recursion in `add_path` is prevented by (add_path.cpp:96):  
  
```  
   if(m_copy_paths.find(p) != m_copy_paths.end())  
      return;  
```  
  
So for some reason the the check for "have we seen this file before" is failing, even though I assume we really do already have it in the list.  Are you able to debug and see what the argument to `add_path` is that causes the issue?  My guess is that there's a file with some sort of screwball name somewhere that is confusing Boost.Filesystem.

---

## Comment 5

> Username: steveire  
> Created at: 2019-11-01 10:32:38 UTC  
> Updated at: 2019-11-01 10:39:29 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-548742068  

Running in docker might be essential to reproducing the issue.  
  
I'm running a 16.04 docker inside a 18.04 host, but it might be possible to reproduce without those exact versions.  
  
Here are the commands that lead to the segfault:  
  
```  
tar -xjf boost_1_71_0.tar.bz2  
cd boost_1_71_0  
./bootstrap.sh   
./b2 tools/bcp/  
mkdir nsboost  
./dist/bin/bcp libs ./nsboost  
```

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-11-01 12:45:19 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-548775079  

The thing is you're talking to a non-linux non-docker user here.  
  
The output will be voluminous, but what happens if you add:  
```  
   std::cout << p << std::endl;  
```  
  
To the start of `bcp_implementation::add_file`, does this show what file is triggering the infinite recursion?  
  
BTW the only other situation I can think of that would lead to this, is if there is a link leading to a circular filesystem path - that should obviously not be the case within the boost tree.

---

## Comment 7

> Username: steveire  
> Created at: 2019-11-01 13:34:10 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-548789239  

Yes, I added content similar to that cout in a few of the methods there. It doesn't seem to be infinite recursion to me, but I was not able to narrow down the test case. It seems to be non-deterministic to some extent.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2019-11-01 15:18:40 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-548827544  

What was the final output before the overflow?

---

## Comment 9

> Username: steveire  
> Created at: 2019-11-14 12:46:17 UTC  
> Updated at: 2019-11-14 12:47:51 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-553872425  

This is not possible to debug interactively through the medium of github comments.  
  
Please try to get access to a linux vm or machine and reproduce it locally with docker.

---

## Comment 10

> Username: steveire  
> Created at: 2019-11-14 12:50:42 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-553873959  

> What was the final output before the overflow?  
  
The final output is non-deterministic. It is a different output depending on how much I try to trim the files that bcp processes.

---

## Comment 11

> Username: steveire  
> Created at: 2019-11-15 15:40:58 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-554409622  

I think what's happening is that the limits of Ubuntu 16.04 are being hit somehow. I can't reproduce this in an 18.04 docker, only a 16.04 one.  
  
I added a cout line to add_path and I got `65049` lines before the segfault. That's suspiciously close to `2^16`, and I think `bcp` adds each path entry to a container. My guess is that the compiler or the std library on 16.04 has a limit to how many items can go into the container, and the 1.71 release hits that limit.  
  
This isn't an infinite recursion problem.

---

## Comment 12

> Username: steveire  
> Created at: 2019-11-15 15:58:00 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-554417241  

This patch excludes enough files to make it possible for me to run bcp on 16.04:  
  
```  
--- a/add_path.cpp  
+++ b/add_path.cpp  
@@ -40,6 +40,9 @@ void bcp_implementation::add_path(const fs::path& p)  
   
 void bcp_implementation::add_directory(const fs::path& p)  
 {  
+   if (p.leaf() == "html")  
+      return;  
+  
    //  
    // Don't add files created by build system:  
    //  
```  
  
This also proves that the issue is somehow about processing too many files. With this patch only 42650 files are processed.

---

## Comment 13

> Username: pdimov  
> Created at: 2019-11-15 16:02:37 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-554419422  

This is probably caused by too many active `fileview`s and running out of memory as a consequence. (Scanning an `html` file and `add_file_dependencies` both call `add_path` while a `fileview` is still live.)

---

## Comment 14

> Username: jzmaddock  
> Created at: 2019-11-25 09:29:00 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-558067807  

Can you please try again with current develop?  I've changed it to a fully non-recursive algorithm, so stack usage is now low.  The catch is that a mighty big list of files gets constructed, so dynamic memory usage goes up to ~ 80Mb if you end up pulling in everything.

---

## Comment 15

> Username: firewave  
> Created at: 2019-11-28 12:27:13 UTC  
> Url: https://github.com/boostorg/bcp/issues/6#issuecomment-559475598  

I applied the changes to Boost 1.70 and my build was successful.
