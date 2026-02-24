# #930 - Better document the requirements for using -fno-exceptions [Closed]

> Username: beewoolie  
> Created at: 2023-08-14 18:42:32 UTC  
> Updated at: 2023-11-24 13:00:42 UTC  
> Closed at: 2023-11-24 13:00:18 UTC  
> Url: https://github.com/boostorg/json/issues/930  

Using boost 1.83 and MacOS Ventura.  This also fails on boost 1.79 though there are a number of other warnings that appear for that version.  My understanding is that the JSON code should build with -fno-exceptions replacing throw with abort().  Is this not true?  
  
```  
gcc --version  
Apple clang version 14.0.3 (clang-1403.0.22.14.1)  
Target: arm64-apple-darwin22.4.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
```  
  
```  
#include <boost/json/src.hpp>  
using namespace boost::json;  
  
  
#if defined (TEST_BJ)  
  
// g++ -std=c++20 -fno-exceptions -I ext/boost/boost -DTEST_BJ -o bj bj.cc  
  
  
#include <stdio.h>  
#include <stdlib.h>  
#include "unix/file.h"  
  
void decode (void const* pv, size_t cb) {  
  auto sv = std::string_view (reinterpret_cast<char const*>(pv), cb);  
  ::printf ("# source %p %p\n", sv.begin (), sv.end ());  
  
  error_code ec;  
  value jv = parse (sv, ec);  
  ::printf ("# parse {%s]\n", ec.message ().c_str ());  
  auto o = jv.if_object ();  
  if (o) {  
    for (auto it = o->begin (); it != o->end  (); ++it){  
      auto sKey = it->key ();  
      auto& value = it->value ();  
      ::printf ("# '%.*s':\n", int (sKey.size ()), sKey.begin ());  
      ::printf ("# key %p %p\n", sKey.begin (), sKey.end ());  
      auto sValue = value.if_string ();  
      if (sValue)  
        ::printf ("# value %p %p\n", sValue->begin (), sValue->end ());  
    }  
  }  
}  
  
  
int main (int argc, char** argv) {  
  
  if (argc < 2) {  
    printf ("#*ER file argument required\n");  
    return 1; }  
  
  auto f = File::map_ro (argv[1]);  // mmap file helper  
  
  decode (f.begin (), f.size ());  
  
  return 0;  
}  
  
```  
  
The compiler session  
  
```  
g++ -std=c++20 -fno-exceptions -I glow/ext/boost/boost -DTEST_BJ -o bj bj.cc  
Undefined symbols for architecture arm64:  
  "boost::throw_exception(std::exception const&, boost::source_location const&)", referenced from:  
      boost::json::detail::throw_system_error(boost::json::error, boost::source_location const*) in bj-815140.o  
      boost::json::detail::throw_system_error(boost::system::error_code const&, boost::source_location const&) in bj-815140.o  
      boost::json::static_resource::do_allocate(unsigned long, unsigned long) in bj-815140.o  
      boost::json::detail::null_resource::do_allocate(unsigned long, unsigned long) in bj-815140.o  
ld: symbol(s) not found for architecture arm64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-08-14 21:00:26 UTC  
> Url: https://github.com/boostorg/json/issues/930#issuecomment-1678053835  

That's actually the intended behaviour. You need to provide your own definition, so that your program does something instead of throwing an exception.  
https://www.boost.org/doc/libs/1_83_0/libs/throw_exception/doc/html/throw_exception.html

---

## Comment 2

> Username: beewoolie  
> Created at: 2023-08-14 21:23:13 UTC  
> Url: https://github.com/boostorg/json/issues/930#issuecomment-1678085977  

Fair enough.  I found a reference to the fact that GCC should replace a throw with abort when -fno-exceptions is present.  There is also this:  
  
Supports -fno-exceptions, detected automatically   
  
in the JSON documentation.  It suggests, to me, that the component will not require me to do anything other than setting -fno-exceptions to disable their use.  That appears not to be the case.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-08-15 07:17:25 UTC  
> Url: https://github.com/boostorg/json/issues/930#issuecomment-1678519062  

Yeah, I agree. It should be more clear on what the user needs to do.

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-11-24 13:00:18 UTC  
> Updated at: 2023-11-24 13:00:42 UTC  
> Url: https://github.com/boostorg/json/issues/930#issuecomment-1825644835  

Fixed in 07efc5e83bdb83fa58074799ef3010a1bebc8431
