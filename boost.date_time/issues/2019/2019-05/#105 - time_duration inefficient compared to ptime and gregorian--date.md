# #105 - time_duration inefficient compared to ptime and gregorian::date [Open]

> Username: erikhydro  
> Created at: 2019-05-29 06:23:22 UTC  
> Updated at: 2019-05-30 14:59:16 UTC  
> Url: https://github.com/boostorg/date_time/issues/105  

compile this program:  
```  
#include <boost/date_time/posix_time/ptime.hpp>  
#include <boost/date_time/posix_time/posix_time_duration.hpp>  
#include <iostream>  
#include <vector>  
#include <chrono>  
  
using namespace boost::posix_time;  
using namespace boost::gregorian;  
using namespace std;  
  
int main(int argc, char *argv[])  
{  
  const int64_t count = 1000000;  
  
  {  
    vector<time_duration> v;  
    v.reserve(count);  
    for (int64_t i=0; i<count; ++i) v.push_back(seconds(i));  
  
    auto start = chrono::high_resolution_clock::now();  
    sort(v.begin(), v.end());  
    auto finish = std::chrono::high_resolution_clock::now();  
    cout << "time_duration sort: " << chrono::duration<double>(finish - start).count() << "s\n";  
  }  
  
  {  
    vector<int64_t> v;  
    v.reserve(count);  
    for (int64_t i=0; i<count; ++i) v.push_back(i);  
  
    auto start = chrono::high_resolution_clock::now();  
    sort(v.begin(), v.end());  
    auto finish = std::chrono::high_resolution_clock::now();  
    cout << "int64_t sort: " << chrono::duration<double>(finish - start).count() << "s\n";  
  }  
  
  {  
    vector<date> v;  
    v.reserve(count);  
    date d(1400,1,1);  
    for (int64_t i=0; i<count; ++i) {  
      v.push_back(d);  
      d+=date_duration(1);  
    }  
  
    auto start = chrono::high_resolution_clock::now();  
    sort(v.begin(), v.end());  
    auto finish = std::chrono::high_resolution_clock::now();  
    cout << "date sort: " << chrono::duration<double>(finish - start).count() << "s\n";  
  }  
  
  {  
    vector<ptime> v;  
    v.reserve(count);  
    ptime p(date(1400,1,1));  
    for (int64_t i=0; i<count; ++i) {  
      v.push_back(ptime(p));  
      p+=hours(5)+minutes(4)+seconds(3);  
    }  
  
    auto start = chrono::high_resolution_clock::now();  
    sort(v.begin(), v.end());  
    auto finish = std::chrono::high_resolution_clock::now();  
    cout << "ptime sort: " << chrono::duration<double>(finish - start).count() << "s\n";  
  }  
  
  return 0;  
}  
```  
  
program output on my machine:  
  
time_duration sort: 3.60364s  
int64_t sort: 0.560309s  
date sort: 0.610657s  
ptime sort: 1.36204s  
  
the question is, what is so special about time_duration making it so unbelievably inefficient? it is even more inefficient than ptime!

---

## Comment 1

> Username: erikhydro  
> Created at: 2019-05-29 06:41:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-496806324  

sorry, I forgot to run the optimizer.  
  
g++ -Wall -O2 boost_time_duration_inefficiency.cpp  
  
time_duration sort: 0.0387317s  
int64_t sort: 0.0153296s  
date sort: 0.0145936s  
ptime sort: 0.0152972s  
  
now the output is more what i would accept. all except time_duration take the same time, since all are somehow simply integers. but time_duration is not. it takes much more time. why?

---

## Comment 2

> Username: jeking3  
> Created at: 2019-05-29 10:43:39 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-496884465  

Two possibilities:  
  
1. `operator <` is more complex,  
2. the copy or move constructor is more complex  
  
I'd look at those two things.  You could also use valgrind's callgrind and kcachegrind to profile and inspect your results and look at the tall pole from sort() on in order to investigate further.  
  
Also adding boost::chrono::duration as a comparison would be interesting.

---

## Comment 3

> Username: erikhydro  
> Created at: 2019-05-29 13:47:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-496943947  

I think it should be assumed that time_duration should internally be implemented as an integer. this should have the consequence that when studying the assembler code for comparing two time_durations, all that happens is the assembler code of "less than compare" for integer type. if additional comparisons and branchings take place, then I assume the code is not meeting the design criteria of being optimized.  
  
Well... this is what I did, copy paste this code to justcompare.cpp:  
  
```  
#include <boost/date_time/posix_time/ptime.hpp>  
  
using namespace boost::posix_time;  
  
bool compare_time_duration(time_duration a, time_duration b)  
{  
  return a < b;  
}  
  
bool compare_int(int a, int b)  
{  
  return a < b;  
}  
```  
  
g++ -Wall -O2 -c justcompare.cpp  
objdump -S --demangle justcompare.o  
  
```  
justcompare.o:     file format elf64-x86-64  
  
  
Disassembly of section .text:  
  
0000000000000000  
<compare_time_duration(boost::posix_time::time_duration, boost::posix_time::time_duration)>:  
   0:   48 8b 17                mov    (%rdi),%rdx  
   3:   48 b9 ff ff ff ff ff    movabs $0x7fffffffffffffff,%rcx  
   a:   ff ff 7f   
   d:   48 8d 04 0a             lea    (%rdx,%rcx,1),%rax  
  11:   48 83 f8 fd             cmp    $0xfffffffffffffffd,%rax  
  15:   77 29                   ja     40  
  17:   48 bf fe ff ff ff ff    movabs $0x7ffffffffffffffe,%rdi  
  1e:   ff ff 7f   
  21:   31 c0                   xor    %eax,%eax  
  23:   48 39 fa                cmp    %rdi,%rdx  
  26:   74 60                   je     88  
  28:   48 8b 36                mov    (%rsi),%rsi  
  2b:   48 01 f1                add    %rsi,%rcx  
  2e:   48 83 f9 fd             cmp    $0xfffffffffffffffd,%rcx  
  32:   77 20                   ja     54  
  34:   48 39 fe                cmp    %rdi,%rsi  
  37:   74 0a                   je     43  
  39:   48 39 f2                cmp    %rsi,%rdx  
  3c:   0f 9c c0                setl   %al  
  3f:   c3                      retq     
  40:   48 8b 36                mov    (%rsi),%rsi  
  43:   48 b9 fe ff ff ff ff    movabs $0x7ffffffffffffffe,%rcx  
  4a:   ff ff 7f   
  4d:   31 c0                   xor    %eax,%eax  
  4f:   48 39 ce                cmp    %rcx,%rsi  
  52:   74 3c                   je     90  
  54:   48 bf 00 00 00 00 00    movabs $0x8000000000000000,%rdi  
  5b:   00 00 80   
  5e:   48 39 fa                cmp    %rdi,%rdx  
  61:   74 35                   je     98  
  63:   48 b9 ff ff ff ff ff    movabs $0x7fffffffffffffff,%rcx  
  6a:   ff ff 7f   
  6d:   48 39 ce                cmp    %rcx,%rsi  
  70:   0f 94 c0                sete   %al  
  73:   48 39 ca                cmp    %rcx,%rdx  
  76:   41 0f 95 c0             setne  %r8b  
  7a:   44 20 c0                and    %r8b,%al  
  7d:   74 21                   je     a0  
  7f:   f3 c3                   repz retq   
  81:   0f 1f 80 00 00 00 00    nopl   0x0(%rax)  
  88:   f3 c3                   repz retq   
  8a:   66 0f 1f 44 00 00       nopw   0x0(%rax,%rax,1)  
  90:   f3 c3                   repz retq   
  92:   66 0f 1f 44 00 00       nopw   0x0(%rax,%rax,1)  
  98:   48 39 d6                cmp    %rdx,%rsi  
  9b:   0f 95 c0                setne  %al  
  9e:   c3                      retq     
  9f:   90                      nop  
  a0:   48 39 ca                cmp    %rcx,%rdx  
  a3:   74 da                   je     7f  
  a5:   48 39 fe                cmp    %rdi,%rsi  
  a8:   75 8f                   jne    39  
  aa:   f3 c3                   repz retq   
  ac:   0f 1f 40 00             nopl   0x0(%rax)  
  
00000000000000b0 <compare_int(int, int)>:  
  b0:   39 f7                   cmp    %esi,%edi  
  b2:   0f 9c c0                setl   %al  
  b5:   c3                      retq     
```  
(i removed the verbose jump target strings from the output to make it more readable)  
  
see the difference between compare_time_duration and compare_int...  
  
I have a bad feeling in my stomach... sorry, something is going very wrong here :-)

---

## Comment 4

> Username: JeffGarland  
> Created at: 2019-05-29 14:16:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-496955914  

Sorry I haven't dug into the thread details in any depth, but be aware that the integers used under ptime and time_duration are 'adapted' to allow for infinities and not-a-date-time values.  That makes the comparisons more complex -- which maybe what you're seeing.

---

## Comment 5

> Username: erikhydro  
> Created at: 2019-05-29 15:18:18 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-496982238  

as seen from my second post, date and ptime have the same performance than sorting a plain int64. but time_duration is way slower. It's true there is this thing with not-a-date-time and infinite values. but for date and ptime it works really well whereas for time_duration it is not good.

---

## Comment 6

> Username: JeffGarland  
> Created at: 2019-05-29 15:37:36 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-496990429  

Right ok.  So the implementation ultimately is in date_time/time_duration.hpp.  It looks like this:  
  
    bool operator<(const time_duration& rhs)  const  
    {  
      return ticks_ <  rhs.ticks_;  
    }  
  
So I'm kinda surprised as well.  I think @jeking3 is onto something looking at the assembly.  If you change your function to pass by const& instead.  And I agree with him I'd like to see what a duration time for std::chrono generates.

---

## Comment 7

> Username: JeffGarland  
> Created at: 2019-05-29 16:58:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-497022078  

Not seeing that chrono can get down to integer either.  Which is also a bit surprising.   
  
https://godbolt.org/z/M2G73J

---

## Comment 8

> Username: JeffGarland  
> Created at: 2019-05-29 17:41:55 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-497038126  

Nope -- forgot the -O flags.  This is more realistic and chrono can get down to an instruction that way.   Pretty sure what you're seeing is the infinities.   These numbers look like the smallest and largest int values available --  which it goes onto compare.  
  
cmp_duration(boost::date_time::subsecond_duration<boost::posix_time::time_duration, 1000l>, boost::date_time::subsecond_duration<boost::posix_time::time_duration, 1000l>):  
        movabs  rax, -9223372036854775808  
        movabs  rsi, 9223372036854775806  
  
https://godbolt.org/z/acxIa8  
  
The only real question is why you're not getting that on ptime.  That might be a bug....

---

## Comment 9

> Username: jeking3  
> Created at: 2019-05-30 14:45:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-497355752  

I wouldn't expect it to be as fast as integer comparisons due to the special type handling.  There may also be cases where 64-bit values are insufficient, given internally the library uses nanoseconds in places.

---

## Comment 10

> Username: JeffGarland  
> Created at: 2019-05-30 14:59:16 UTC  
> Url: https://github.com/boostorg/date_time/issues/105#issuecomment-497360577  

I agree -- what I'm seeing in the assembly makes perfect sense given the special value handling -- and as mentioned I question the speed of ptime.  Of course if you're a truly ambitious user you recreate the duration types with a non-adapted number value -- no real instructions on how to do that unfortunately.  
  
I think the real answer here is to start moving towards supporting inter operation with std::chrono duration types as part of a broader strategy to support users in converting to std::chrono in c++20.  Seems to me we can pretty trivially support things like:  
  
  ptime t(...);  
  t+= std::milliseconds(10);  
    
Maybe even time_duration could be explicitly constructable from chrono::duration<U, T> -- will have to think about it more.
