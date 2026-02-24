# #193 - Clang 10 UBSan: boost/archive/iterators/escape.hpp:85:12: runtime error: applying non-zero offset 1 to null pointer [Closed]

> Username: ostash  
> Created at: 2020-04-07 07:43:31 UTC  
> Updated at: 2023-11-22 15:11:36 UTC  
> Closed at: 2023-11-22 15:11:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/193  

Hello,  
  
Following code  
  
```cpp  
#include <boost/archive/iterators/ostream_iterator.hpp>  
#include <boost/archive/iterators/xml_escape.hpp>  
  
#include <iostream>  
#include <string>  
  
int main()  
{  
  const std::string s = "test";  
  
  std::copy(boost::archive::iterators::xml_escape<const char*>(s.c_str()),  
            boost::archive::iterators::xml_escape<const char*>(s.c_str() + s.size()),  
            boost::archive::iterators::ostream_iterator<char>(std::cout));  
}  
```  
  
when compiled with Clang 10 with UBSan:  
  
> $ clang++ -o boostarchive boostarchive.cpp -fsanitize=undefined   
> $ UBSAN_OPTIONS="print_stacktrace=1" ./boostarchive  
  
produces the following error:  
  
```  
boost/archive/iterators/escape.hpp:85:12: runtime error: applying non-zero offset 1 to null pointer  
    #0 0x42be87 in boost::archive::iterators::escape<boost::archive::iterators::xml_escape<char const*>, char const*>::increment() (boostarchive+0x42be87)  
    #1 0x42bd01 in void boost::iterators::iterator_core_access::increment<boost::archive::iterators::xml_escape<char const*> >(boost::archive::iterators::xml_escape<char const*>&) (boostarchive+0x42bd01)  
    #2 0x42a724 in boost::iterators::detail::iterator_facade_base<boost::archive::iterators::xml_escape<char const*>, char, boost::iterators::single_pass_traversal_tag, char, long, false, false>::operator++() (boostarchive+0x42a724)  
    #3 0x42a1da in boost::archive::iterators::ostream_iterator<char> std::__copy_move<false, false, std::input_iterator_tag>::__copy_m<boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char> >(boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char>) (boostarchive+0x42a1da)  
    #4 0x429d9f in boost::archive::iterators::ostream_iterator<char> std::__copy_move_a<false, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char> >(boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char>) (boostarchive+0x429d9f)  
    #5 0x4298aa in boost::archive::iterators::ostream_iterator<char> std::__copy_move_a2<false, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char> >(boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char>) (boostarchive+0x4298aa)  
    #6 0x42946b in boost::archive::iterators::ostream_iterator<char> std::copy<boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char> >(boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char>) (boostarchive+0x42946b)  
    #7 0x4292a9 in main (boostarchive+0x4292a9)  
    #8 0x3cdc61ed1c in __libc_start_main (/lib64/libc.so.6+0x3cdc61ed1c)  
    #9 0x403434 in _start (boostarchive+0x403434)  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior boost/archive/iterators/escape.hpp:85:12 in  
test  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2020-04-07 14:39:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/193#issuecomment-610425908  

Right - this behavior was intentionally introduced in order to ...  I don't remember now. I can't be just "fixed".  It needs to be investigated at a deeper level.  Combing through the code, one can find a couple of instances of this.  Basically every time there is a reinterpret_cast.  It's possible that application of reinterpretation_cast might be used to hide this warning.  
  
BTW - the behavior might be undefined, but I don't think it is ever wrong or leads to unexpected results.

---

## Comment 2

> Username: ostash  
> Created at: 2020-04-09 06:01:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/193#issuecomment-611345342  

I'd really like to help, but need some hints where to start looking...

---

## Comment 3

> Username: robertramey  
> Created at: 2020-04-09 14:19:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/193#issuecomment-611553765  

This is a very subtle issue which was tricky to make work.  So it's not really a "fix" it's a mini research project.  If you take that approach you might find it interesting.  That is it's a "small" problem, but richer than first meets the eye. So you might find it fun to spend some time on as an interesting exercise.  First of all I would find the file boost/archive/iterators/escape.hpp in the git hub repository.  This will show you the context.  Then display the history of the file - the sequence of historical changes.  Then you'll have a good start to understanding where the problem came from, what it is and perhaps the next evolutionary step to make things better.  Good Luck!

---

## Comment 4

> Username: ostash  
> Created at: 2020-04-09 15:36:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/193#issuecomment-611595746  

It looks like simple one-line change is enough to fix the issue:  
  
```  
--- boost/archive/iterators/escape.hpp.orig     2020-04-09 10:28:13.000000000 -0500  
+++ boost/archive/iterators/escape.hpp  2020-04-09 10:28:50.140358237 -0500  
@@ -82,7 +82,7 @@  
     }  
  
    void increment(){  
-        if(++m_bnext < m_bend){  
+        if(m_bnext && ++m_bnext < m_bend){  
             m_current_value = *m_bnext;  
             return;  
         }  
```

---

## Comment 5

> Username: jeking3  
> Created at: 2022-04-26 02:06:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/193#issuecomment-1109221296  

Might I suggest instead:  
```  
--- boost/archive/iterators/escape.hpp.orig     2020-04-09 10:28:13.000000000 -0500  
+++ boost/archive/iterators/escape.hpp  2020-04-09 10:28:50.140358237 -0500  
@@ -82,7 +82,7 @@  
     }  
  
    void increment(){  
-        if(++m_bnext < m_bend){  
+        if(m_bnext != m_bend && ++m_bnext < m_bend){  
             m_current_value = *m_bnext;  
             return;  
         }  
```  
  
This was enough to fix some identified ubsan issues in date_time where it leverages serialization.  
  
```  
../../boost/archive/iterators/escape.hpp:85:12: runtime error: applying non-zero offset 1 to null pointer  
    #0 0x7f4a8923eab1 in boost::archive::iterators::escape<boost::archive::iterators::xml_escape<char const*>, char const*>::increment() /boost/libs/date_time/../../boost/archive/iterators/escape.hpp:85:12  
    #1 0x7f4a8923e93b in void boost::iterators::iterator_core_access::increment<boost::archive::iterators::xml_escape<char const*> >(boost::archive::iterators::xml_escape<char const*>&) /boost/libs/date_time/../../boost/iterator/iterator_facade.hpp:556:13  
    #2 0x7f4a8923db0e in boost::iterators::detail::iterator_facade_base<boost::archive::iterators::xml_escape<char const*>, char, boost::iterators::single_pass_traversal_tag, char, long, false, false>::operator++() /boost/libs/date_time/../../boost/iterator/iterator_facade.hpp:666:13  
    #3 0x7f4a8923d843 in boost::archive::iterators::ostream_iterator<char> std::__copy_move<false, false, std::input_iterator_tag>::__copy_m<boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char> >(boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char>) /usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/bits/stl_algobase.h:311:48  
    #4 0x7f4a8923d4f6 in boost::archive::iterators::ostream_iterator<char> std::__copy_move_a<false, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char> >(boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char>) /usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/bits/stl_algobase.h:403:14  
    #5 0x7f4a8923d18e in boost::archive::iterators::ostream_iterator<char> std::__copy_move_a2<false, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char> >(boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char>) /usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/bits/stl_algobase.h:441:3  
    #6 0x7f4a8923b122 in boost::archive::iterators::ostream_iterator<char> std::copy<boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char> >(boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::xml_escape<char const*>, boost::archive::iterators::ostream_iterator<char>) /usr/bin/../lib/gcc/x86_64-linux-gnu/9/../../../../include/c++/9/bits/stl_algobase.h:473:14  
    #7 0x7f4a89238c28 in boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>::save(char const*) /boost/libs/date_time/../../boost/archive/impl/xml_oarchive_impl.ipp:94:5  
    #8 0x7f4a8923834e in boost::archive::basic_xml_oarchive<boost::archive::xml_oarchive>::write_attribute(char const*, char const*) /boost/libs/date_time/../../boost/archive/impl/basic_xml_oarchive.ipp:83:19  
    #9 0x7f4a89237df9 in boost::archive::basic_xml_oarchive<boost::archive::xml_oarchive>::init() /boost/libs/date_time/../../boost/archive/impl/basic_xml_oarchive.ipp:244:5  
    #10 0x42e87f in boost::archive::xml_oarchive::xml_oarchive(std::ostream&, unsigned int) /boost/libs/date_time/../../boost/archive/xml_oarchive.hpp:125:13  
    #11 0x42c980 in main /boost/libs/date_time/../../libs/date_time/test/posix_time/testtime_serialize.cpp:57:25  
    #12 0x7f4a88b050b2 in __libc_start_main /build/glibc-sMfBJT/glibc-2.31/csu/../csu/libc-start.c:308:16  
    #13 0x40ad0d in _start (/boost/bin.v2/libs/date_time/test/testtime_serialize_xml.test/clang-linux-10/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testtime_serialize_xml+0x40ad0d)  
  
SUMMARY: UndefinedBehaviorSanitizer: nullptr-with-nonzero-offset ../../boost/archive/iterators/escape.hpp:85:12 in  
```
