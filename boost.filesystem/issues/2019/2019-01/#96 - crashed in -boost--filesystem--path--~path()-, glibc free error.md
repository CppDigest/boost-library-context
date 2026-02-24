# #96 - crashed in "boost::filesystem::path::~path()", glibc free error [Closed]

> Username: wind2412  
> Created at: 2019-01-01 06:50:02 UTC  
> Updated at: 2019-01-01 16:16:39 UTC  
> Closed at: 2019-01-01 15:15:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/96  

os: `ubuntu 16.04`  
g++ --version:  
```  
g++ (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609  
Copyright (C) 2015 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
boost version: 1.66/1.69 both have this problem, I didn't test the others  
  
**I built boost myself**, using g++ 5.4, and following [build](https://stackoverflow.com/questions/12578499/how-to-install-boost-on-ubuntu), and then facing a crash same as [crash](https://stackoverflow.com/questions/53549304/invalid-free-boostfilesystempathpath), glibc free error  
  
A simple code slice can reproduce this problem.  
  
```c++  
#include <iostream>  
#include <vector>  
#include <boost/filesystem.hpp>  
#include <boost/filesystem/operations.hpp>  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/xml_parser.hpp>  
#include <sstream>  
#include <locale>  
#include <codecvt>  
  
using namespace std;  
  
std::wstring utf8_to_wstring (const std::string& str)  
{  
	std::wstring_convert<std::codecvt_utf8<wchar_t>> myconv;  
	return myconv.from_bytes(str);  
}  
std::wstring utf8_to_wstring (std::string && str)  
{  
	std::wstring_convert<std::codecvt_utf8<wchar_t>> myconv;  
	return myconv.from_bytes(str);  
}  
  
int main(int argc, char *argv[])   
{  
	wstring pwd = utf8_to_wstring(boost::filesystem::initial_path<boost::filesystem::path>().string());  
	// get xml  
	wstring config_xml = pwd + L"/config.xml";  
	if(!boost::filesystem::exists(config_xml)) {  // crash here  
	}  
}  
```  
  
```  
g++ -std=c++11 1.cc -lboost_filesystem -lboost_system  
./a.out  
  
root@be387067c6b0:~ # ./a.out  
[1]    45287 segmentation fault  ./a.out  
  
gdb...  
>>> bt  
#0  __GI___libc_free (mem=0xe) at malloc.c:2951  
#1  0x0000000000404872 in boost::filesystem::path::~path() ()  
#2  0x00000000004033a0 in main ()  
>>>  
```  
  
Also, valgrind will give some errors  
  
```c++  
root@be387067c6b0:~ # valgrind ./a.out  
==45357== Memcheck, a memory error detector  
==45357== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.  
==45357== Using Valgrind-3.11.0 and LibVEX; rerun with -h for copyright info  
==45357== Command: ./a.out  
==45357==  
==45357== Syscall param stat(file_name) contains uninitialised byte(s)  
==45357==    at 0x56E0BE5: _xstat (xstat.c:35)  
==45357==    by 0x4E43AD9: boost::filesystem::detail::status(boost::filesystem::path const&, boost::system::error_code*) (in /usr/lib/x86_64-linux-gnu/libboost_filesystem.so.1.58.0)  
==45357==    by 0x404935: boost::filesystem::exists(boost::filesystem::path const&) (in /root/a.out)  
==45357==    by 0x403393: main (in /root/a.out)  
==45357==  
==45357== Syscall param stat(file_name) points to unaddressable byte(s)  
==45357==    at 0x56E0BE5: _xstat (xstat.c:35)  
==45357==    by 0x4E43AD9: boost::filesystem::detail::status(boost::filesystem::path const&, boost::system::error_code*) (in /usr/lib/x86_64-linux-gnu/libboost_filesystem.so.1.58.0)  
==45357==    by 0x404935: boost::filesystem::exists(boost::filesystem::path const&) (in /root/a.out)  
==45357==    by 0x403393: main (in /root/a.out)  
==45357==  Address 0x0 is not stack'd, malloc'd or (recently) free'd  
==45357==  
==45357== Conditional jump or move depends on uninitialised value(s)  
==45357==    at 0x4048E2: boost::filesystem::exists(boost::filesystem::file_status) (in /root/a.out)  
==45357==    by 0x404941: boost::filesystem::exists(boost::filesystem::path const&) (in /root/a.out)  
==45357==    by 0x403393: main (in /root/a.out)  
==45357==  
==45357== Invalid free() / delete / delete[] / realloc()  
==45357==    at 0x4C2F24B: operator delete(void*) (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)  
==45357==    by 0x404871: boost::filesystem::path::~path() (in /root/a.out)  
==45357==    by 0x40339F: main (in /root/a.out)  
==45357==  Address 0xe is not stack'd, malloc'd or (recently) free'd  
==45357==  
==45357==  
==45357== HEAP SUMMARY:  
==45357==     in use at exit: 72,735 bytes in 2 blocks  
==45357==   total heap usage: 141 allocs, 140 frees, 89,620 bytes allocated  
==45357==  
==45357== LEAK SUMMARY:  
==45357==    definitely lost: 31 bytes in 1 blocks  
==45357==    indirectly lost: 0 bytes in 0 blocks  
==45357==      possibly lost: 0 bytes in 0 blocks  
==45357==    still reachable: 72,704 bytes in 1 blocks  
==45357==         suppressed: 0 bytes in 0 blocks  
==45357== Rerun with --leak-check=full to see details of leaked memory  
==45357==  
==45357== For counts of detected and suppressed errors, rerun with: -v  
==45357== Use --track-origins=yes to see where uninitialised values come from  
==45357== ERROR SUMMARY: 4 errors from 4 contexts (suppressed: 0 from 0)  
root@be387067c6b0:~ #  
```  
  
I would like to know how to workaround this.  
I'm looking forward to the reply, thank you!

---

## Comment 1

> Username: Lastique  
> Created at: 2019-01-01 15:15:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/96#issuecomment-450737259  

I cannot reproduce this neither on develop nor on 1.69. Tried with gcc 5.5.0, 6.5.0 and 8.2.0 on Kubuntu 18.10. Valgrind also shows no errors.  
  
My guess is that this is probably code miscompilation which have been fixed in newer compiler versions. Try updating your compiler or playing with compiler options.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-01-01 16:16:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/96#issuecomment-450740707  

The call stack contains `(in /usr/lib/x86_64-linux-gnu/libboost_filesystem.so.1.58.0)` which is an indication that the program has been linked to the preinstalled Boost 1.58.0 instead of the built 1.6x one.
