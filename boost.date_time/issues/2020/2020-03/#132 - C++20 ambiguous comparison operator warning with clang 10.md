# #132 - C++20 ambiguous comparison operator warning with clang 10 [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2020-03-12 13:05:03 UTC  
> Updated at: 2020-07-09 15:09:37 UTC  
> Closed at: 2020-07-09 15:09:36 UTC  
> Url: https://github.com/boostorg/date_time/issues/132  

Hi,  
  
When using C++20 with clang >= 10, we get the following warnings:  
  
```  
#include "boost/date_time.hpp"  
  
bool f()  
{  
    return boost::posix_time::ptime(boost::gregorian::date(2028, 4, 17)) == boost::posix_time::ptime(boost::gregorian::date(2028, 4, 17));  
}  
```  
  
```  
<source>:5:74: error: ISO C++20 considers use of overloaded operator '==' (with operand types 'boost::posix_time::ptime' and 'boost::posix_time::ptime') to be ambiguous despite there being a unique best viable function [-Werror,-Wambiguous-reversed-operator]  
    return boost::posix_time::ptime(boost::gregorian::date(2028, 4, 17)) == boost::posix_time::ptime(boost::gregorian::date(2028, 4, 17));  
           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ ^  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/celibs/boost_1_71_0/boost/date_time/time.hpp:126:10: note: ambiguity is between a regular call to this operator and a call with the argument order reversed  
    bool operator==(const time_type& rhs) const  
         ^  
1 error generated.  
Compiler returned: 1  
```  
  
See a live example on compiler explorer: https://godbolt.org/z/6yYb7C  
  
Do you know how to fix this ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created at: 2020-03-12 13:09:38 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-598176050  

This actually boils down to this pattern:  
  
```  
template <typename T> struct A  
{  
    bool operator==(const T&) const;  
};  
  
struct B : public A<B> {};  
  
bool f()  
{  
    return B() == B();  
}  
```

---

## Comment 2

> Username: JeffGarland  
> Created at: 2020-03-12 13:49:04 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-598195466  

Interesting.  Looks like 10 has a partial implementation of the consistent compare (aka spaceship) -- which may be part of the issue.  I've been thinking the operators will probably need to be redone for spaceship anyway.  Of the cuff I'm not sure the best way to solve it -- would need to experiment...  
  
https://clang.llvm.org/cxx_status.html#cxx20

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created at: 2020-03-12 13:54:20 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-598198076  

Actually I have asked clarification directly on the clang user mailing list. My message is currently on-hold since I am not member of the list, but you will be able to see the mail thread here http://lists.llvm.org/pipermail/cfe-users/2020-March/thread.html as soon as it will be unblocked.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created at: 2020-03-12 13:56:48 UTC  
> Updated at: 2020-03-12 13:59:24 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-598199334  

And in order to fix my own problem, I worked it around by not comparing ptime, but by comparing std::time_t instead, using boost::posix_time::to_time_t. This is definitely not nice, but in my case it's acceptable (it was in a unit test) to silence this specific case of warning with ptime while still keeping it for other types which might need fixing as well.

---

## Comment 5

> Username: JeffGarland  
> Created at: 2020-03-12 14:33:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-598221497  

Thanks -- will be curious on the response.  Will be trying to build the latest clang in my environment.  
  
But actually looks the the following will work for the moment.   
  
    namespace boost::posix_time {  
  
    bool   
    operator==(const ptime& lhs, const ptime& rhs)  
    {  
     return lhs == rhs;  
    }  
   }  
  
  
https://godbolt.org/z/7RyxJc

---

## Comment 6

> Username: Romain-Geissler-1A  
> Created at: 2020-03-12 23:32:31 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-598477197  

Message posted: http://lists.llvm.org/pipermail/cfe-users/2020-March/001705.html

---

## Comment 7

> Username: JeffGarland  
> Created at: 2020-03-13 00:30:08 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-598490238  

Actually I can answer some of the questions there: c++20 is baked - the final draft was adopted at the Prague meeting.  That doesn't mean that there can't be bug fixes.  Also, it's entirely likely that what's in the design isn't fully reflected in the compiler yet.  And it's possible that this was the intent.  I might be able to get the attention of someone that can acutally answer the question...stay tuned.

---

## Comment 8

> Username: JeffGarland  
> Created at: 2020-03-13 11:30:34 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-598677414  

@BRevzin has indicated to me that the committee is aware of this issue and is looking to resolve it.  Will keep the issue open to document.  Note that I will want to rework these operators for 20 to work with spaceship anyway.

---

## Comment 9

> Username: Romain-Geissler-1A  
> Created at: 2020-03-19 11:31:30 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-601129609  

Hi,  
  
FYI Richard Smith who implemented that in clang confirmed as well that "various implementers" raised that to the committee and that maybe a Defect Report might retro-actively change that: http://lists.llvm.org/pipermail/cfe-users/2020-March/001707.html We have to wait.  
  
In the meantime, I choose to enclose all boost date time headers with pragmas to ignore it as there is isn't much I can do about it to fix this, while I still want to have an opportunity to fix this in our own company code.  
  
Cheers,  
Romain

---

## Comment 10

> Username: JeffGarland  
> Created at: 2020-03-19 15:48:36 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-601255181  

Thanks for the update -- Richard didn't really say how to make the changes, but I'm not surprised.  I'm willing to change the code to be compatible with the new rules -- since I don't understand them and I don't yet have the latest clang in my environment it could be some time before that occurs - almost certainly after the next boost release.

---

## Comment 11

> Username: Romain-Geissler-1A  
> Created at: 2020-03-19 18:28:14 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-601343053  

In the case I saw for real, the problem usually was that existing code did not have reversable primary comparison operator, which becomes a problem with C++ 20.  
  
I found https://brevzin.github.io/c++/2019/07/28/comparisons-cpp20/#reversing-primary-operators which sheds some light.

---

## Comment 12

> Username: JeffGarland  
> Created at: 2020-03-22 16:37:48 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602236490  

I guess the thing that is slightly different in the example is that this is A == int whereas we're seeing the issue with ptime == ptime.    
  
This is a fuller accounting (edited to remove dups) of the scope from the current regression on the develop branch.  Note that in part this extends up to boost/operators.hpp, but the issue seems to be mostly with gregorian::date, date_type, and ptime.  
  
Warnings: teeks99-dkr-dc11-2a - date_time / clang-linux-11~c++2a  
Rev 0779a468248ba0c479c3404ca3ee1ba7239aae31 / Tue, 17 Mar 2020 08:27:36 +0000  
  
testclocks  
  
../boost/date_time/dst_rules.hpp:163:25: warning: ISO C++20 considers use of overloaded operator '==' (with operand types 'const boost::date_time::dst_calculator<boost::gregorian::date, boost::posix_time::time_duration>::date_type' (aka 'const boost::gregorian::date') and 'const boost::date_time::dst_calculator<boost::gregorian::date, boost::posix_time::time_duration>::date_type') to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]  
  
testcustom_time_zone  
  
../libs/date_time/test/local_time/testcustom_time_zone.cpp:64:41: warning: ISO C++20 considers use of overloaded operator '==' (with operand types '**boost::posix_time::ptime**' and 'boost::posix_time::ptime') to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]  
../boost/date_time/dst_rules.hpp:163:25: warning: ISO C++20 considers use of overloaded operator '==' (with operand types 'const boost::date_time::dst_calculator<boost::gregorian::date, boost::posix_time::time_duration>::date_type' (aka 'const boost::gregorian::date') and 'const boost::date_time::dst_calculator<boost::gregorian::date, boost::posix_time::time_duration>::date_type') to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]  
  
testdate  
  
../libs/date_time/test/gregorian/testdate.cpp:35:36: warning: ISO C++20 considers use of overloaded operator '==' (with operand types **'boost::gregorian::date' and 'boost::gregorian::date'**) to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]  
../boost/**operators.hpp**:162:83: warning: ISO C++20 considers use of overloaded operator '==' (with operand types 'const boost::gregorian::date' and 'const boost::gregorian::date') to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]  
  
testperiod  
  
../libs/date_time/test/gregorian/testperiod.cpp:17:26: warning: ISO C++20 considers use of overloaded operator '==' (with operand types 'boost::gregorian::date' and 'boost::gregorian::date') to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]  
  
testtime_formatters  
  
../boost/date_time/time_system_split.hpp:145:24: warning: ISO C++20 considers use of overloaded operator '==' (with operand types 'const boost::posix_time::simple_time_rep::date_type' (aka 'const boost::gregorian::date') and 'const boost::posix_time::simple_time_rep::date_type') to be ambiguous despite there being a unique best viable function [-Wambiguous-reversed-operator]

---

## Comment 13

> Username: brevzin  
> Created at: 2020-03-22 17:04:46 UTC  
> Updated at: 2020-03-22 17:09:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602240151  

@JeffGarland The simplest fix might be to do:  
  
```diff  
template <typename T>  
struct base_time {  
-    bool operator==(T const&) const;  
+    friend bool operator==(T const&, T const&);  
};  
  
struct ptime : base_time<ptime> { };  
  
bool f(ptime const& a, ptime const& b) {  
    return a == b;  
}  
```  
  
I'm not super familiar with the date_time library, but I'm guessing you don't actually need to compare a `base_time<ptime>` with a `ptime` anywhere, this is more just a way of getting comparisons between `ptime`s to work?  
  
Boost.Operators already injects its operators as friends like this, so `!=` works. [Demo](https://godbolt.org/z/24VL2H). [Conformance View](https://godbolt.org/z/_NbJLM).  
  
gcc actually accepts the status quo anyway, because it does a hybrid of C++20/C++17 rules.

---

## Comment 14

> Username: JeffGarland  
> Created at: 2020-03-22 18:03:18 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602248329  

Nice, thank you.  I will certainly do that for the interim.  Of course, the I think the real goal for 20 should be to shift to operator<=>.  
  
> guessing you don't actually need to compare a `base_time<ptime>` with a `ptime`  
  
correct.  
  
> gcc actually accepts the status quo anyway  
  
Yes, that's my primary development compiler, so even with 2a on I don't see it without looking at the boost regression tests.

---

## Comment 15

> Username: brevzin  
> Created at: 2020-03-22 19:35:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602260896  

>  Of course, the I think the real goal for 20 should be to shift to operator<=>.  
  
It's a little annoying right now because clang implements `<=>`, libc++ provides `<compare>` but not the corresponding macro.   
  
But basically I think you want:  
  
```cpp  
template <class T, class time_system>  
class base_time  
#if !defined(__cpp_lib_three_way_comparison)  
    : private boost::less_than_comparable<T, boost::equality_comparable<T>>  
#endif  
{  
  friend constexpr bool operator==(T const& lhs, T const& rhs) {  
    return time_system::is_equal(lhs.time_, rhs.time_);  
  }  
  
#ifdef __cpp_lib_three_way_comparison  
  friend constexpr auto operator<=>(T const& lhs, T const& rhs) {  
    return time_system::compare(lhs.time_, rhs.time_);  
  }  
#else  
  friend constexpr bool operator<(T const& lhs, T const& rhs) {  
    return time_system::is_less(lhs.time_, rhs.time_);  
  }  
#endif  
};  
```  
Assuming you control all the `time_system`s. If not, then probably want to constrain (with concepts) `operator<=>` on the presence of `time_system::compare` and still provide the `operator<` and the inheritance from `less_than_comparable`. The constrained `operator<=>` would be a better match than the unconstrained `operator<` so it'd be preferred when viable (this is what I did for, e.g., `std::optional`).

---

## Comment 16

> Username: JeffGarland  
> Created at: 2020-03-22 20:07:54 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602264791  

So what I'm finding is that there are complications with making the friend operator== due to the extra template params in the real impl.  It's probably doable, but it seems like the easiest thing is to push the friend operator== to `gregorian::date` and `posix_time::ptime` and worry about the more generic change later.  
  
for the moment I think all the time_systems are in the library since I never really offered docs or explained how to customize them -- I was never 100% convinced of the design there.   So as far as I'm aware no one has custom version.  I'm going to need to get my clang environment updated to really attack the 2a update beyond the simple fix.  Oh, and there's higher goals in 2a that would use operator<=> -- since you'd surely like to be able to say things like: `gregorian::date == std::sys_days`.  
  
And thanks again for the assist in working this out.

---

## Comment 17

> Username: Romain-Geissler-1A  
> Created at: 2020-03-22 20:14:25 UTC  
> Updated at: 2020-03-22 20:16:46 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602265640  

> It's a little annoying right now because clang implements <=>, libc++ provides <compare> but not the corresponding macro.  
  
I have a clang 10 which is compiled with the sources of the clang 10 branch from 5th March, and it does define the macro:  
  
```  
rgeissler@ncerndobedev6097:~> /opt/1A/toolchain/x86_64-v20/bin/clang++ -std=c++20 -dM -E -x c++ - </dev/null | grep __cpp_impl_three_way_comparison  
#define __cpp_impl_three_way_comparison 201907L  
```  
  
You seem to be checking for `__cpp_lib_three_way_comparison` which as far as I understood is for the standard lib implementation support, while you really need the compiler implementation support, which is checked with `__cpp_impl_three_way_comparison` (or maybe I misunderstood these feature macros).

---

## Comment 18

> Username: JeffGarland  
> Created at: 2020-03-22 20:28:33 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602267432  

Yes, I think @BRevzin is wanting to use the library support in header <compare> and the language macro would only signal that the compiler is ready and not the std lib.  
  
@Romain-Geissler-1A I committed a change to the dt_ambig_equal branch ( 2 files pull #140 ) that will hopefully clear this up at the moment.  I'll be able to see since the boost test robots include clang10 in 2a mode but you could also give it a try.

---

## Comment 19

> Username: Romain-Geissler-1A  
> Created at: 2020-03-22 20:51:02 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602271147  

Thanks !  
  
I have just checked in the diff of pull request #140 in our own internal code base. It will take some hours to rebuild everything with an updated boost. In the meantime we added pragmas diagontics to silence the warning, I removed them as well. I will let you know if it works for us now.

---

## Comment 20

> Username: JeffGarland  
> Created at: 2020-03-22 21:33:25 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602276772  

@Romain-Geissler-1A just realized that if you are working with 1.72 date_time and you only took the 2 updates you might have to remove the BOOST_CXX14_CONSTEXPR from the signatures.  I'm working off the develop branch which put constexpr support into the date-time core -- 1.72 doesn't have that so you might get errors associated with calling non-constexpr functions from constexpr.

---

## Comment 21

> Username: Romain-Geissler-1A  
> Created at: 2020-03-22 21:39:34 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602277572  

Indeed boost itself failed to build:  
  
```  
[2020-03-22T20:53:45.362Z] ./boost/date_time/posix_time/ptime.hpp: In function 'constexpr bool boost::posix_time::operator==(const boost::posix_time::ptime&, const boost::posix_time::ptime&)':  
  
[2020-03-22T20:53:45.362Z] ./boost/date_time/posix_time/ptime.hpp:66:45: error: call to non-'constexpr' function 'static bool boost::date_time::counted_time_system<time_rep>::is_equal(const time_rep_type&, const time_rep_type&) [with time_rep = boost::date_time::counted_time_rep<boost::posix_time::millisec_posix_time_system_config>; boost::date_time::counted_time_system<time_rep>::time_rep_type = boost::date_time::counted_time_rep<boost::posix_time::millisec_posix_time_system_config>]'  
```  
I don't see any boost 1.73 alpha or beta tarball yet, so I won't test this, and put back the pragmas on the version 1.72.

---

## Comment 22

> Username: JeffGarland  
> Created at: 2020-03-22 22:37:46 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602287498  

We're not that far yet on 1.73, but its super easy for you to make it compatible - in greg_date.hpp and ptime.hpp where the patch is, just remove the BOOST_CXX14_CONSTEXPR (4 changes) so for example:  
```diff  
-  friend BOOST_CXX14_CONSTEXPR  
+  friend  
   bool operator==(const date& lhs, const date& rhs);  
```

---

## Comment 23

> Username: Romain-Geissler-1A  
> Created at: 2020-03-23 00:00:49 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602301515  

Ok compilation of our own code works with the patch, tests as well ;)

---

## Comment 24

> Username: JeffGarland  
> Created at: 2020-03-23 00:15:38 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-602303888  

Thanks, that's good to know.  msvc seems happy, but the initial CI with clang/gcc hasn't kicked off yet (and doesn't have clang10) -- so it would have been another 24 hours, at least, to make it up to develop and have the big regression run for me to see that its fixed.   
  
I expect this will should be merged for 1.73 along with the constexpr and 'no library' updates.  First big date_time update in awhile. I'll open a new issue for supporting c++20 operator<=> fully.

---

## Comment 25

> Username: JeffGarland  
> Created at: 2020-07-09 15:09:36 UTC  
> Url: https://github.com/boostorg/date_time/issues/132#issuecomment-656185146  

Closing -- shipped with 1.73
