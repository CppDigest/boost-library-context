# #2 Fix read of unitialized memory warnings and add minor C++11 improvements... [Merged]

> Username: apolukhin  
> Created at: 2014-07-29 12:37:14 UTC  
> Updated at: 2015-04-02 02:28:27 UTC  
> Merged at: 2015-04-02 02:28:27 UTC  
> Closed at: 2015-04-02 02:28:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/2  

.... This is related to the [Ticket #9407](https://svn.boost.org/trac/boost/ticket/9407)

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-10-01 14:17:40 UTC  
> Url: https://github.com/boostorg/uuid/pull/2#issuecomment-57470651  

1. I think deleting copy constructor/assignment with BOOST_DELETED_FUNCTION is more preferable than using noncopyable.  
2. std::rand address and this_ptr are constant and are unlikely to improve entropy. The previous code used heap and garbage data to improve entropy quality.  
3. I'm not sure you actually fixed the valgrind warning. The memory will still be uninitialized if CryptAcquireContextA fails. Also in this case you call CryptReleaseContext on an invalid handle.  
4. There's no /dev/urandom on Windows, so no point in trying to open it. Even worse, one could create a file with that name and effectively influence the RNG.  
  
I'd suggest the following:  
1. Change the implementation to use CryptoAPI on Windows; remove the /dev/urandom-related code on that platform.  
2. To avoid using uninitialized memory one can use high resolution timer, pid and thread id as basic entropy sources. On Windows QueryPerformanceCounter can be used as a timer, on UNIX-like systems clock_gettime with CLOCK_MONOTONIC_RAW, CLOCK_MONOTONIC or CLOCK_REALTIME - whichever succeeds first. If everything fails, std::rand can be used.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-10-01 16:22:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/2#issuecomment-57493201  

Made some changes to fix noted issues. I've leaved noncopyable (it has some advantages in C++03 with boost::is_copyable trait).  
  
Usage of heap and garbage data were the places valgrind was complying on so I've left mixing in std::rand and this_ptr address (does not look like they'll do worse, but maybe they can do a little bit better.)  
  
Please take a look at the changes. I have a few more questions:  
- Is it ok to add -lrt dependency on Linux (required for using clock_gettime)?  
- Windows requires AdvAPI32 library to link with. Shall I make this library autolink on Windows?

---

## Comment 3

> Username: Lastique  
> Created_at: 2014-10-01 17:56:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/2#issuecomment-57507529  

The QueryPerformanceCounter part is not correct as it will always fill the array with the result of QueryPerformanceFrequency (i.e. a constant). And you lose most of the counter bits. Given that one loop is more or less constant time, the resulting entropy won't be good. I think something like this would be better:  
  
```  
struct  
{  
  LARGE_INTEGER_ n1;  
  DWORD_ n2, n3;  
  std::time_t n4;  
  void* n5;  
} data;  
QueryPerformanceCounter(&data.n1);  
data.n2 = GetCurrentThreadId();  
data.n3 = GetCurrentProcessId();  
data.n4 = std::time(NULL);  
data.n5 = this;  
sha.process_bytes((unsigned char const*)&data, sizeof(data));  
```  
  
Note that no loop is needed. And yes, AdvAPI32 should be auto-linked.  
  
As for the UNIX part, yes, clock_gettime can be problematic. It is also absent in OS X but can be replaced with gettimeofday (which, actually doesn't need -lrt). Maybe something like the following will be enough:  
  
```  
struct  
{  
  int n1;  
  pid_t n2;  
  timeval n3;  
  void* n4;  
} data;  
// Not sure about these macro tests - taken from man page  
#if _SVID_SOURCE || _BSD_SOURCE || _XOPEN_SOURCE >= 500 || _XOPEN_SOURCE && _XOPEN_SOURCE_EXTENDED  
data.n1 = ::random();  
#else  
data.n1 = std::rand();  
#endif  
data.n2 = getpid();  
gettimeofday(&data.n3);  
data.n4 = this;  
sha.process_bytes((unsigned char const*)&data, sizeof(data));  
```  
  
The point of these changes is to make sure that the RNG produces different values from one operator() call to another, from one application run to another (including for different simultaneously running applications) and from one system reboot to another.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2014-10-02 08:33:40 UTC  
> Url: https://github.com/boostorg/uuid/pull/2#issuecomment-57598348  

Done (entropy from `std::rand()` and some of the time functions is already mixed-in lower in code).  
  
To sum up the changes of all the commits in this pull request:  
- removed insecure attempt to read from "/dev/urandom" on Windows  
- added gathering random data from Windows Crypto context when possible + autolink with advapi32.lib  
- added gathering entropy from system specific sources (pid, process id, thread id)  
- fixed valgrind warnings (read of uninitialized memory)  
- C++11 `noexcept` and `boost::noncopyable` are now used

---

## Comment 5

> Username: Lastique  
> Created_at: 2014-10-02 08:56:12 UTC  
> Url: https://github.com/boostorg/uuid/pull/2#issuecomment-57600600  

You missed  
  
```  
   #include <sys/types.h>  
   #include <unistd.h>  
```  
  
for getpid(). Also, std::time(0) duplicates gettimeofday() in the UNIX case, it's only needed for Windows. I still see no point in seeding the address of std::rand but oh well. Other than that looks ok to me.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2014-10-02 09:48:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/2#issuecomment-57606063  

Done. Tested on GCC-4.6(c++03, c++03 + valgrind and c++11), Clang-4.3 and MSVC-11.0. Works well.

---

## Comment 7

> Username: apolukhin  
> Created_at: 2014-11-27 11:13:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/2#issuecomment-64777324  

Any news on this issue? When the fix will be merged?

---
