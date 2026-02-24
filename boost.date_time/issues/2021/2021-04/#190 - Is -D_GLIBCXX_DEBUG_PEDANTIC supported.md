# #190 - Is -D_GLIBCXX_DEBUG_PEDANTIC supported? [Open]

> Username: maflcko  
> Created at: 2021-04-29 08:47:36 UTC  
> Updated at: 2021-04-30 17:47:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/190  

I was wondering if this library supports `-D_GLIBCXX_DEBUG_PEDANTIC` builds. Currently I am seeing a failure. Steps to reproduce:  
  
```  
$ cat 2.cpp   
  
#include <boost/algorithm/hex.hpp>  
#include <boost/date_time.hpp>  
  
int main() {  
  std::string hex("31393730300b");  
  std::string str;  
  boost::algorithm::unhex(hex.begin(), hex.end(), std::back_inserter(str));  
  
  const std::locale loc(  
      std::locale::classic(),  
      new boost::posix_time::time_input_facet("%Y-%m-%dT%H:%M:%SZ"));  
  std::istringstream iss(str);  
  iss.imbue(loc);  
  boost::posix_time::ptime ptime(boost::date_time::not_a_date_time);  
  iss >> ptime;  
}  
  
  
  
  
$ g++ -D_GLIBCXX_DEBUG -D_GLIBCXX_DEBUG_PEDANTIC -g -std=c++17 -Wall 2.cpp -o exe && valgrind ./exe   
==5147== Memcheck, a memory error detector  
==5147== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==5147== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info  
==5147== Command: ./exe  
==5147==   
/usr/include/c++/9/bits/streambuf_iterator.h:140:  
In function:  
    std::istreambuf_iterator<_CharT, _Traits>::char_type   
    std::istreambuf_iterator<_CharT, _Traits>::operator*() const [with   
    _CharT = char; _Traits = std::char_traits<char>;   
    std::istreambuf_iterator<_CharT, _Traits>::char_type = char]  
  
Error: attempt to dereference an end-of-stream istreambuf_iterator (this is   
a GNU extension).  
  
Objects involved in the operation:  
    iterator @ 0x0x1fff000300 {  
      type = std::istreambuf_iterator<char, std::char_traits<char> >;  
    }  
==5147==   
==5147== Process terminating with default action of signal 6 (SIGABRT): dumping core  
==5147==    at 0x4A9118B: raise (raise.c:51)  
==5147==    by 0x4A70858: abort (abort.c:79)  
==5147==    by 0x48F0148: ??? (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28)  
==5147==    by 0x123E17: std::istreambuf_iterator<char, std::char_traits<char> >::operator*() const (streambuf_iterator.h:140)  
==5147==    by 0x12570E: boost::date_time::format_date_parser<boost::gregorian::date, char>::parse_month(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::date_time::parse_match_result<char>&) const (format_date_parser.hpp:473)  
==5147==    by 0x120E3C: boost::date_time::time_input_facet<boost::posix_time::ptime, char, std::istreambuf_iterator<char, std::char_traits<char> > >::get(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, std::ios_base&, boost::posix_time::ptime&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, bool) const (time_facet.hpp:1031)  
==5147==    by 0x11C461: boost::date_time::time_input_facet<boost::posix_time::ptime, char, std::istreambuf_iterator<char, std::char_traits<char> > >::get(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, std::ios_base&, boost::posix_time::ptime&) const (time_facet.hpp:936)  
==5147==    by 0x119033: std::basic_istream<char, std::char_traits<char> >& boost::posix_time::operator>><char, std::char_traits<char> >(std::basic_istream<char, std::char_traits<char> >&, boost::posix_time::ptime&) (posix_time_io.hpp:80)  
==5147==    by 0x11405C: main (2.cpp:15)

---

## Comment 1

> Username: maflcko  
> Created at: 2021-04-29 09:33:54 UTC  
> Updated at: 2021-04-30 09:20:24 UTC  
> Url: https://github.com/boostorg/date_time/issues/190#issuecomment-829085208  

<!-- export DEBIAN_FRONTEND=noninteractive && apt update && apt install curl wget htop git vim ccache  build-essential libtool autotools-dev automake pkg-config bsdmainutils clang llvm clang-format libboost-date-time-dev -y -->  
  
I get the same error message, but different trace for the hex test case: `3134353009310909090909090909090909090909090909`.  
  
I tested the boost versions:  
* 1.71  
* 1.74

---

## Comment 2

> Username: JeffGarland  
> Created at: 2021-04-30 17:46:12 UTC  
> Updated at: 2021-04-30 17:47:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/190#issuecomment-830256957  

I don't believe the compiler option is relevant here -- the format an the input doesn't match.  If there's a bug here it's that the issue isn't detected sooner and an exception raised sooner -- with the cost of lowered performance.  It's also the case that much of this depends on locale code for doing the work -- although apparently not in the particular case.
