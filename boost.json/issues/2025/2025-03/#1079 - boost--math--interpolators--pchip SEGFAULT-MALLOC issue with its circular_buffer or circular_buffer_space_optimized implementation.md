# #1079 - boost::math::interpolators::pchip SEGFAULT/MALLOC issue with its circular_buffer or circular_buffer_space_optimized implementation ? [Closed]

> Username: rodv92  
> Created at: 2025-03-30 19:22:33 UTC  
> Updated at: 2025-03-30 19:28:24 UTC  
> Closed at: 2025-03-30 19:28:23 UTC  
> Url: https://github.com/boostorg/json/issues/1079  

Issue description :  
  
When pushing back the initial values to the circular buffers of x,y before the call to the pchip constructor, if the number of data points  
is **less than the circular buffer capacity** set during the circular buffers constructor call, subsequent push_backs calls of the pchip instance will eventually lead to :   
  
- a NaN at evaluation  
  
that seems to coincide with the upper boundary of the elements of the initial circular buffer, **even when some push_backs have been done after the call to the pchip constructor.** , so that the evaluation does not lay at the boundary of the data anymore.  
  
- a segfault (for circular_buffer) or a malloc error (for circular_buffer_space_optimized) after a certain amount of push backs or evaluations.  
  
Issue Reproduction:  
  
Using a ramp function of the form y=x as it does not obfuscate the issue and does not involve cubic hermite logic.  
If the capacity set here : boost::circular_buffer_space_optimized<double>(20); is greater than the push back count (10) in the first for loop, the issue will appear. The greater the discrepancy between the allocated elements and the total push back counts, the greater the probability of segfault, that seems then to happens when the circular buffer cycles (here at index 20)  
  
Seems the issue was already at least in boost 1.75  
  
It doesn't seems that it is a big issue as capacity can be set at runtime to match the number of elements that one got to work with  
before calling the pchip constructor, but maybe throwing an exception would be nice to prevent head scratching.  
and maybe a note in the documentation : https://live.boost.org/doc/libs/1_87_0/libs/math/doc/html/math_toolkit/pchip.html  
  
CODE TO REPRODUCE THE ISSUE :  
  
#include <boost_1_82_0/circular_buffer.hpp>  
#include <boost_1_82_0/math/interpolators/pchip.hpp>  
  
using boost::math::interpolators::pchip;  
  
#include <math.h>  
#include <bits/stdc++.h>  
  
  
  
#include <boost_1_82_0/circular_buffer.hpp>  
#include <boost_1_82_0/math/interpolators/pchip.hpp>  
  
using boost::math::interpolators::pchip;  
  
#include <math.h>  
#include <bits/stdc++.h>  
  
  
  
int main()  
{  
    
  
    boost::circular_buffer_space_optimized<double> initial_x = boost::circular_buffer_space_optimized<double>(20);  
    boost::circular_buffer_space_optimized<double> initial_y = boost::circular_buffer_space_optimized<double>(20);  
    // CAPACITY SET AT 20  
       
      
    // NUMBER OF PUSH BACKS BEFORE std::move and CONSTRUCTOR CALL = 10  
    for(uint32_t i=0; i<10; i++)  
    {  
        printf("push back count (before constructor call) i = %lu\n",i+1);  
  
        initial_x.push_back(double(i+0.5f));  
        initial_y.push_back(double(i+0.5f));  
    }  
      
  
    printf("before pchip constructor call\n");  
  
  
    //my_pchip  = new pchip<boost::circular_buffer<double>>(std::move(initial_x),std::move(initial_y));  
    boost::math::interpolators::pchip<boost::circular_buffer_space_optimized<double>> my_pchip = pchip<boost::circular_buffer_space_optimized<double>>(std::move(initial_x),std::move(initial_y));  
  
    //DebugPrint(dbgstr("pchip ptr"),my_pchip,0);  
      
    printf("after pchip constructor call\n");  
  
    for(uint32_t i=10; i<100; i++)  
    {  
        printf("push back count (after constructor call) i = %lu\n",i+1);  
        my_pchip.push_back(double(i+0.5f),double(i+0.5f));              
        printf("eval at x = %f\n",double(i-2.f +0.3f));  
        double y = my_pchip(double(i-2.f +0.3f));  
        printf("y = %f\n",y);  
          
      
    }  
  
    return 0;  
}  
  
  
DEBUG OUTPUT 👍   
  
push back count (before constructor call) i = 1  
push back count (before constructor call) i = 2  
push back count (before constructor call) i = 3  
push back count (before constructor call) i = 4  
push back count (before constructor call) i = 5  
push back count (before constructor call) i = 6  
push back count (before constructor call) i = 7  
push back count (before constructor call) i = 8  
push back count (before constructor call) i = 9  
push back count (before constructor call) i = 10  
before pchip constructor call  
after pchip constructor call  
push back count (after constructor call) i = 11  
eval at x = 8.300000  
y = 8.300000  
push back count (after constructor call) i = 12  
eval at x = 9.300000  
y = nan  
push back count (after constructor call) i = 13  
eval at x = 10.300000  
y = nan  
push back count (after constructor call) i = 14  
eval at x = 11.300000  
y = 11.300000  
push back count (after constructor call) i = 15  
eval at x = 12.300000  
y = 12.300000  
push back count (after constructor call) i = 16  
eval at x = 13.300000  
y = 13.300000  
push back count (after constructor call) i = 17  
eval at x = 14.300000  
y = 14.300000  
push back count (after constructor call) i = 18  
eval at x = 15.300000  
y = 15.300000  
push back count (after constructor call) i = 19  
  
**Program received signal SIGSEGV, Segmentation fault.  
__GI___libc_free (mem=0x3ff0000000000000) at malloc.c:3102**

---

## Comment 1

> Username: rodv92  
> Created at: 2025-03-30 19:28:23 UTC  
> Url: https://github.com/boostorg/json/issues/1079#issuecomment-2764709738  

wrong repo sorry.
