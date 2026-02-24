# #1006 - bg::projections::detail::epsg_to_parameters causes excessive compile times [Closed]

> Username: sigbjorn  
> Created at: 2022-05-21 15:22:17 UTC  
> Updated at: 2025-08-05 08:26:08 UTC  
> Closed at: 2022-11-22 14:09:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1006  

Hi,  
  
First of all, thanks for this excellent library!   
  
# Intro  
  
Just to notify about an issue that might be of interest for those using the  projections part of boost geometry,  
limited to the epsg projection engine.  
  
To be clear: This is not an issue about the correctness of the code, rather practical implications regarding excessive compile times.  
  
The function in question is the  boost::geometry::projections::detail::epsg_to_parameters(), that does the job of converting an epsg code to parameters that describes the transformation.  
  
E.g. given epsg code (an int) return back boost::geometry::srs::parameters<>.  
  
This is realized using a static array inside the mentioned function, declarative and clear to read.  
  
  
## the issue  
  
The only issue is the time that the compiler need in order to resolve the expressions in the static table,  
 chained call,   
starting with parameters<>(projection)(param1)....(param1) // the call operator is overloaded  
  
Compile times for pre gcc 12.1 was already high (several minutes),  
 but with the new gcc 12.1 it becomes > 30 minutes (33 minutes on ryzen 9 5950X series, only one core used).  
  
On godbolt, the mininal sample below works for pre 12.1 compilers, but time-out on 12.1.  
  
```c++  
#include <boost/geometry/srs/projections/dpar.hpp>  
#include <boost/geometry/srs/projections/epsg.hpp>  
  
namespace test {  
    size_t spend_massive_compile_time(int epsg_code) {  
        auto dp = boost::geometry::projections::detail::epsg_to_parameters(epsg_code);  
        return dp.size();  
    }  
}  
  
int main(int argc, char **argv) {  
    return test::spend_massive_compile_time(32631); // should return 6  
}  
```   
  
## Possible solutions/workarounds.  
  
### Simplify the expressions in the table  
  
I tried to rework the static table, trying to avoid using chained expressions,   
rather use use plain vector<parameter<T>> {}.  
  
It was kind of promising, 10 times faster!  
 - so 3 minutes instead of 30 minutes.   
  
Looking at the data that goes into the static table, it's basically code,'numbers', strongly typed,  
so moving along in a direction where the data that drives the resulting parameters are simple/fast compile-time types,   
it might be possible to get down to 'zero' compile-time,   
- at the cost of some run-time. (because the construction of the wanted object must be done at runtime)  
  
E.g. creating the parameters only when really asked for,   
by means of typed constants/tuples, that are cheap during compile time.  
  
A local test with initial approach, using the same table definitions,   
 reduces the compile time from 33 minutes to 3 minutes. (promising)   
So it illustrates that it works, even though 3 minutes to compile a table of approx 5k rows is still excessive.  
  
Further progress on this shows that compiling a simple POD approach,  
takes only 0.8 seconds. (compared to 33 minutes gcc 12.1, or 3 minutes pre gcc 12).   
  
**So approx 2000 times faster than the current situation for gcc 12.1** (and any boost geometry version, is my guess).  
  
I think with a little work on this approach it might be worth considering,   
unless there are other more obvious solutions around that I have overlooked.  
  
Sketches for this approach looks like:  
  
```c++  
#include <boost/geometry/srs/projections/dpar.hpp>  
  
  
namespace boost::geometry::projections::detail {  
    namespace epsg_data {  
        /**  
         * @brief simplest possible pod to capture a parameter definition  
         */  
        struct epsg_p_def {  
            int e_type;///< unique enum type, one of name_xxx enums.  
            int e_val{0}; ///< sub-enum type, one of value_xxx enums  
            int n_args{0};///< number of floating point args, dependent on e_type,e_val  
            double args[7];//most parameters are 1, towgs84 3 or 7  
        };  
        /**  
         * @brief  pod to keep one epsg def, a set of epsg_p_defs.  
         */  
        struct epsg_def {  
            epsg_p_def p_defs[13];// currently number of p_defs are 13(!)  
        };  
        struct epsg_entry {  
            int code;  
            epsg_def parameters;  
        };  
        using namespace srs::dpar;  
  
        static const epsg_entry arr[] = {  
            {2000, {{{proj,proj_tmerc},{lat_0,0,1,{0}},{lon_0,0,1,{-62}},{k,0,1,{0.9995000000000001}},{x_0,0,1,{400000}},{y_0,0,1,{0}},{ellps,ellps_clrk80},{units,units_m},{no_defs}}}},  
            {2001, {{{proj,proj_tmerc},{lat_0,0,1,{0}},{lon_0,0,1,{-62}},{k,0,1,{0.9995000000000001}},{x_0,0,1,{400000}},{y_0,0,1,{0}},{ellps,ellps_clrk80},{units,units_m},{no_defs}}}},  
            {2002, {{{proj,proj_tmerc},{lat_0,0,1,{0}},{lon_0,0,1,{-62}},{k,0,1,{0.9995000000000001}},{x_0,0,1,{400000}},{y_0,0,1,{0}},{ellps,ellps_clrk80},{towgs84,0,7,{725,685,536,0,0,0,0}},{units,units_m},{no_defs}}}},  
            {2003, {{{proj,proj_tmerc},{lat_0,0,1,{0}},{lon_0,0,1,{-62}},{k,0,1,{0.9995000000000001}},{x_0,0,1,{400000}},{y_0,0,1,{0}},{ellps,ellps_clrk80},{towgs84,0,7,{72,213.7,93,0,0,0,0}},{units,units_m},{no_defs}}}},  
            {2004, {{{proj,proj_tmerc},{lat_0,0,1,{0}},{lon_0,0,1,{-62}},{k,0,1,{0.9995000000000001}},{x_0,0,1,{400000}},{y_0,0,1,{0}},{ellps,ellps_clrk80},{towgs84,0,7,{174,359,365,0,0,0,0}},{units,units_m},{no_defs}}}},  
            {2005, {{{proj,proj_tmerc},{lat_0,0,1,{0}},{lon_0,0,1,{-62}},{k,0,1,{0.9995000000000001}},{x_0,0,1,{400000}},{y_0,0,1,{0}},{ellps,ellps_clrk80},{units,units_m},{no_defs}}}},  
          // some more thousands rows follows here  
        };  
    }  
}  
  
```  
  
  
### Make the table/instantiation part of a .cpp  file   
  
If the table was created as part of the compilation of the boost library itself, instead of header-only for this part, that  might work.  
Maybe in combination with the speedup approach, 30 minutes is still some time, for a single file.  
  
A variant is to control the inline/static part with a compile-time define, so that affected users can choose to compile the table-part in a separate unit (avoiding repeated compilations of the stuff that takes time)  
Drawbacks with this approach is clearly that it breaks header-only, so the 'hybrid' user def constant specified approach could work, helping out the users that are affected.

---

## Comment 1

> Username: vissarion  
> Created at: 2022-11-22 14:09:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1006#issuecomment-1323736033  

Testing on `Ubuntu 22.04 LTS` with a few compilers and boost `1.80`  
  
| compiler    | time (sec) |  
| ----------- | -----------: |  
| g++9     | 5.11       |  
| g++10   | 5.49       |  
| g++11     | 5.11     |  
| g++12   |   541.63     |  
| clang++11     | 2.72       |  
| clang++12   |  2.92      |  
| clang++13     |  2.80    |  
| clang++14   |  2.89      |  
| clang++15   |  3.07      |  
  
it seems that the compile time is reasonable and stable across versions with one exception: `g++12` (the version in the table is `12.1.0`). After that I compiled with `g++12.2.0` and the time was 5.88 secs. Therefore, it seems that the current issue is related to a bug of `g++12.1.0` fixed in `g++12.2.0`.   
  
I am closing this issue. If there is something still unclear please reopen it.

---

## Comment 2

> Username: addy90  
> Created at: 2023-11-07 15:04:16 UTC  
> Updated at: 2023-11-07 15:09:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1006#issuecomment-1798793116  

I can confirm this issue with GCC 13.2.0, compile time is back to be very large over 30 minutes.  
The referenced issue above from the NOAA-OWP/ngen project also mentions GCC 13.1.1 being extremely slow.  
With disabled optimization (-O0) compilation time is fine.  
  
Since this issue is recurring in GCC, it might be worth revising this part of the code.

---

## Comment 3

> Username: sigbjorn  
> Created at: 2023-11-07 19:09:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1006#issuecomment-1799622797  

For info:  
  
We still use the workaround to keep compile-time to a minimum, using the approach as outlined above,   
  
Source files :  
  
https://gitlab.com/shyft-os/shyft/-/blob/master/cpp/shyft/core/epsg_fast.h  
https://gitlab.com/shyft-os/shyft/-/blob/master/cpp/shyft/core/epsg_fast.cpp  
  
But would be very happy to drop them at anytime it becomes clear that there is a stable(over some time) low-compile time solution becomes available, one way or the other.  
  
Hopefully the compilers solves the problem, otherwise there might be several similar approaches to the above that might give a solution that compiles in a few seconds.

---

## Comment 4

> Username: tinko92  
> Created at: 2025-08-05 06:27:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/1006#issuecomment-3153665265  

@addy90 @sigbjorn A fix to this issue has been merged to the current develop branch (not in master), it would be interesting to see whether it works for your use cases.

---

## Comment 5

> Username: addy90  
> Created at: 2025-08-05 08:26:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1006#issuecomment-3154080248  

Hey @tinko92,  
Thank you for providing your new solution for this issue!  
  
I tried it out in my application locally. I applied the develop branch, removed the compile flag exceptions for the srs/epsg part of the code, and it compiled fine on GCC 15.1 and Clang 20 without any issues with compiler optimizations enabled. The  release binary was also ~0.4 MB smaller than before in my case.  
  
Can't wait to see this in a coming boost stable release! I am carrying a workaround with me since ever then. 😄  
Sincere Thank you!
