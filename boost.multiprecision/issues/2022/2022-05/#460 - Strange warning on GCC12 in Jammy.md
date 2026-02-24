# #460 - Strange warning on GCC12 in Jammy [Closed]

> Username: ckormanyos  
> Created at: 2022-05-16 20:12:55 UTC  
> Updated at: 2022-05-18 05:11:29 UTC  
> Closed at: 2022-05-18 05:11:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/460  

I recently found strange behavior when using GCC12 on Jammy with some Multiprecision code.  
  
In one of my projects that uses Multiprecision rather heavily, I found [this report](https://github.com/ckormanyos/wide-integer/runs/6457392593?check_suite_focus=true#step:5:38). It mentions what appears to be a memory/STL problem instantiated through [this line](https://github.com/boostorg/multiprecision/blob/93c15efe56604e7657284b1df4d92d35d3365313/include/boost/multiprecision/cpp_int.hpp#L2107). I got this in [this CI](https://github.com/ckormanyos/wide-integer/actions/runs/2333700626).  
  
When I change from [this line](https://github.com/boostorg/multiprecision/blob/93c15efe56604e7657284b1df4d92d35d3365313/include/boost/multiprecision/cpp_int.hpp#L2107) to [this line](https://github.com/boostorg/multiprecision/blob/78afb27ca3bd96537157f1cf9cd0ff577d3537fa/include/boost/multiprecision/cpp_int.hpp#L2107), the warning is gone and my CI (which has `-Werror`) runs [green](https://github.com/ckormanyos/wide-integer/actions/runs/2333934270).  
  
I'm not overly concerned with warnings when I subjectively _decide_ to treat warnings as errors. What concerns me, however, is the nature of the warning, as it mentions negative offsets in a memory-copy-ish built-in. I have so far found two such instances. I'm thnking some preprocessing in GCC12 might go haywire, but I'm not sure. The other error (warning) I found was in my own (non-boost) code.  
  
We can work around or dive deeper. i suspec the warnings might be bogus, but memory offsets are a sensitive area.  
  
There is no real clear action to take here. Some options include either working around it in `cpp_int.hpp` (via different code, as in my branch) or trying to figure out if it's bogus and `#pragma`-ing out the warning.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-05-17 07:41:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/460#issuecomment-1128525252  

Do you also see this with just:  
  
```  
#include <string>  
  
int main()  
{  
   std::string r;  
   r = "0";  
   return r.size() != 1;  
}  
```  
  
It looks like "not our issue" and should be reported upstream?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-05-18 05:11:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/460#issuecomment-1129573463  

Yup. Got it.
