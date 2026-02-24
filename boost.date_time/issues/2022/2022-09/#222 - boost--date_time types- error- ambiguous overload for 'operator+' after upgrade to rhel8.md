# #222 - boost::date_time types: error: ambiguous overload for 'operator+' after upgrade to rhel8 [Open]

> Username: cameron-reed-1  
> Created at: 2022-09-19 13:56:06 UTC  
> Updated at: 2022-12-02 16:18:22 UTC  
> Url: https://github.com/boostorg/date_time/issues/222  

Hello,   
We are porting our product to RHEL8. Our old configuration is this:  
  
$ cat /etc/redhat-release  
Red Hat Enterprise Linux Server release 7.9 (Maipo)  
$ gcc --version  
gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44)  
  
and our NEW environent:  
$ cat /etc/redhat-release  
Red Hat Enterprise Linux release 8.6 (Ootpa)  
  
$ gcc --version  
gcc (GCC) 8.5.0 20210514 (Red Hat 8.5.0-10)  
  
In this new environment I am getting conflicts between boost and roguewave (SourcePro).   
Roguewave: SourceProEd2021.1-Linux64 (el8)  
Boost: boost_1_80_0  
  
The EXACT same setup builds fine on rhel7 but not on rhel8 so i believe the new compiler is discovering this ambiguity whereas the old gcc compiler was able to resolve it.   
  
I boiled it down to having two headers in the source file: here is the full source file:  
$cat aes_CamsTest.cc  
// START INCLUDES  
#include "boost/date_time/posix_time/posix_time.hpp"  
#include "rw/tools/bustring.h"  
  
And here is the compile command:  
/bin/g++ -DBUILD_64 -D_RWCONFIG_12s -O3 -Wformat -Wformat-security -fPIC -MD -fstack-protector -isystem/homes/reedca/cots/boost_1_80_0 -isystem/apps64/pds/cots/rogue/SourceProEd2021.1-Linux64.el8/includes -isystem/apps64/pds/cots/rogue/SourceProEd12021.1-Linux64.el8/includes/rw/config -m64 -std=c++14  -c /tms/pdsgit/reedca/pds-server-4/system/develop/aes/source/aes_CamsTest.cc -o /tms/pdsgit/reedca/pds-server-4/system/develop/aes/obj/Linux/aes_CamsTest.o  
  
The new error is here and is repeated several times for the various different boost date_time types:  
In file included from /homes/reedca/cots/boost_1_80_0/boost/date_time/gregorian/greg_duration_types.hpp:15,  
                 from /homes/reedca/cots/boost_1_80_0/boost/date_time/gregorian/gregorian_types.hpp:22,  
                 from /homes/reedca/cots/boost_1_80_0/boost/date_time/posix_time/posix_time_config.hpp:18,  
                 from /homes/reedca/cots/boost_1_80_0/boost/date_time/posix_time/posix_time_system.hpp:13,  
                 from /homes/reedca/cots/boost_1_80_0/boost/date_time/posix_time/ptime.hpp:12,  
                 from /homes/reedca/cots/boost_1_80_0/boost/date_time/posix_time/posix_time.hpp:15,  
                 from /tms/pdsgit/reedca/pds-server-4/system/develop/aes/source/aes_CamsTest.cc:2:  
/homes/reedca/cots/boost_1_80_0/boost/date_time/adjust_functors.hpp: In instantiation of 'boost::date_time::month_functor<date_type>::duration_type boost::date_time::month_functor<date_type>::get_offset(const date_type&) const [with date_type = boost::gregorian::date; boost::date_time::month_functor<date_type>::duration_type = boost::gregorian::date_duration]':  
/homes/reedca/cots/boost_1_80_0/boost/date_time/date_duration_types.hpp:67:16:   required from 'constexpr boost::date_time::months_duration<base_config>::duration_type boost::date_time::months_duration<base_config>::get_offset(const date_type&) const [with base_config = boost::gregorian::greg_durations_config; boost::date_time::months_duration<base_config>::duration_type = boost::gregorian::date_duration; boost::date_time::months_duration<base_config>::date_type = boost::gregorian::date]'  
/homes/reedca/cots/boost_1_80_0/boost/date_time/posix_time/date_duration_operators.hpp:33:37:   required from here  
/homes/reedca/cots/boost_1_80_0/boost/date_time/adjust_functors.hpp:71:110: error: ambiguous overload for 'operator+' (operand types are 'boost::gregorian::greg_year' and 'int')  
       const typename ymd_type::year_type year(static_cast<typename ymd_type::year_type::value_type>(ymd.year + wi.add(f_)));  
                                                                                                     ~~~~~~~~~^~~~~~~~~~~~  
/homes/reedca/cots/boost_1_80_0/boost/date_time/adjust_functors.hpp:71:110: note: candidate: 'operator+(int, int)' <built-in>  
In file included from /tms/pdsgit/reedca/pds-server-4/system/develop/aes/source/aes_CamsTest.cc:3:  
/apps64/pds/cots/rogue/SourceProEd2021.1-Linux64.el8/includes/rw/tools/bustring.h:4214:1: note: candidate: 'RWBasicUString operator+(RWUChar16, const RWBasicUString&)'  
 operator+(RWUChar16 lhs, const RWBasicUString& rhs)  
 ^~~~~~~~  
/apps64/pds/cots/rogue/SourceProEd2021.1-Linux64.el8/includes/rw/tools/bustring.h:4228:1: note: candidate: 'RWBasicUString operator+(RWUChar32, const RWBasicUString&)'  
 operator+(RWUChar32 lhs, const RWBasicUString& rhs)  
 ^~~~~~~~  
  
Please advise:  
Thanks.   
  
Cameron Reed  
Staff Software Engineer  
Wabtec Corporation  
321-591-5131

---

## Comment 1

> Username: cameron-reed-1  
> Created at: 2022-09-23 13:10:43 UTC  
> Url: https://github.com/boostorg/date_time/issues/222#issuecomment-1315941515  

Hello,   
  
I also opened cases with roguewave and redhat.  RogueWave (SourcePro) was able to replicate the issue and sent this reply:  
  
_**"1. In general, it should be possible to use SourcePro and Boost in the same application. However both Boost and SourcePro are large projects with a large number of components in them. There may be conflicts (like we're seeing above) between specific Boost and SourcePro components.  
  
2. The conflict between Boost Date/Time and SourcePro is caused by implicit conversions of integer types and C++ rules for overload resolution. To resolve those sorts of errors, the ambiguity needs to be resolved. From the Boost side, this could be addressed by updating the code to perform an explicit conversion to the types expected by the operator+ overload being invoked. This would not have an impact on API compatibility, but will increase the verbosity of the code. To resolve the conflict from the SourcePro side, the RWBasicUString construct that takes an RWSize_T will need to be marked as explicit, which will prevent the implicit conversion from being considered in overload resolution. Note that this breaks source-compatibility."**_  
  
#End of their comment  
  
They also sent a test program that removed them (roguewave) from the process. According to them, this test program will also result in the issue.  Please check it out and let me know what action might be taken and a time-frame.   
  
Thanks.   
Cameron Reed.   
  
//// test program  
#include <boost/date_time/posix_time/posix_time.hpp>  
   
class MyInt  
{  
public:  
MyInt(int x) : x_(x) {}  
   
private:  
int x_;  
   
friend MyInt operator+(unsigned short lhs, const MyInt &rhs);  
};  
   
MyInt operator+(unsigned short lhs, const MyInt& rhs) {  
return rhs;  
}_

---

## Comment 2

> Username: cameron-reed-1  
> Created at: 2022-09-23 14:08:49 UTC  
> Url: https://github.com/boostorg/date_time/issues/222#issuecomment-1315941525  

By the way, I did make a mistake in my original compiler command that I sent. I had it as c++14 and that does not build on rhel7. In the meantime, I also had moved the SourcePro area. So, the last command line I used, which ran on both rhel7 and rhel8 (but errored out due to above issue on rhel8) is this:  
  
/bin/g++ -DBUILD_64 -D_RWCONFIG_12s -O3 -Wformat -Wformat-security -fPIC -MD -fstack-protector -isystem/homes/reedca/cots/boost_1_80_0 -isystem/home/reedca/cots/sourcePro2021.1_Linux/includes -isystem/home/reedca/cots/sourcePro2021.1_Linux/includes/rw/config -m64 -std=c++11  -c /tms/pdsgit/reedca/pds-server-4/system/develop/aes/source/aes_CamsTest.cc -o /tms/pdsgit/reedca/pds-server-4/system/develop/aes/obj/Linux/aes_CamsTest.o

---

## Comment 3

> Username: cameron55445-1  
> Created at: 2022-12-02 16:18:22 UTC  
> Url: https://github.com/boostorg/date_time/issues/222#issuecomment-1335494100  

Hello,   
I wanted to follow up on this issue. I have implemented a temporary fix to some boost header files to resolve this issue.  
  
- In the boost_date time code, there are instances where the ymd variables are added or subtracted from an integer and this was creating the ambiguity.  
- I simply did a standard typecast to unsigned short in those cases.  
- Two files changed.  
- adjust_functors.hpp and Gregorian_calendar.ipp.  
- typecast whenever a ymd value was added or involved in arithmetic with an integer. Here is an example:  
- unsigned short a = static_cast<unsigned short>((14-ymd.month)/12);  
- changed to:  
- unsigned short a = static_cast<unsigned short>((14-(unsigned short)ymd.month)/12);  
  
Attaching the changed files here.   
Note, .hpp and .ipp files cannot be pasted here. I added a 'txt' suffix to both to allow pasting.   
  
[gregorian_calendar.ipp.txt](https://github.com/boostorg/date_time/files/10142634/gregorian_calendar.ipp.txt)  
[adjust_functors.hpp.txt](https://github.com/boostorg/date_time/files/10142631/adjust_functors.hpp.txt)
