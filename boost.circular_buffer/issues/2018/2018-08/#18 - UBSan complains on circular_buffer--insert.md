# #18 - UBSan complains on circular_buffer::insert [Closed]

> Username: ldionne  
> Created at: 2018-08-22 16:27:41 UTC  
> Updated at: 2019-04-23 13:34:33 UTC  
> Closed at: 2019-04-23 13:34:33 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/18  

UBSan complains on presumably valid code:  
  
```c++  
#include <boost/circular_buffer.hpp>  
#include <vector>  
  
int main(int argc, const char * argv[]) {  
    boost::circular_buffer<uint8_t> cbuff(99);  
    std::vector<uint8_t> blah = { 'b', 'l', 'a', 'h', '\0'};  
      
    // Wrap around  
    for(unsigned i = 0; i < 99999; ++i) {  
        cbuff.insert(cbuff.end(), blah.begin(), blah.end());  
    }  
    return 0;  
}  
```  
  
Build and run:  
  
```  
$ clang++ -std=c++11 -fsanitize=undefined t.cpp -o t.exe  
$ ./t.exe  
/usr/local/include/boost/circular_buffer/base.hpp:2358:18: runtime error: addition of unsigned offset to 0x7fa96dc02b6f overflowed to 0x7fa96dc02b11  
```  
  
The complaint is on `boost/circular_buffer/base.hpp:2358`, where:  
  
```c++  
2358:         return p + (n < (m_end - p) ? n : n - capacity());  
```  
  
With:  
  
```  
(lldb) p/x (void*)p  
(void *) $1 = 0x0000000100f0024f  
(lldb) p/x (void*)n  
(void *) $2 = 0x0000000000000005  
(lldb) p/x (void*)m_end  
(void *) $3 = 0x0000000100f00253  
```  
  
So `m_end - p = 4`, `n = 5`, the condition is `false`, and we are adding `p + n - capacity()`, and the `p+n` part is overflowing `m_end`.

---

## Comment 1

> Username: sandeshghanta  
> Created at: 2018-09-19 12:46:53 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/18#issuecomment-422789129  

Can you please tell for what version of boost and clang you are getting that error. I have tried reproducing the bug with boost 1.67.0 and clang version 3.8.0-2ubuntu4 but have failed. The code seems to be working perfectly.

---

## Comment 2

> Username: aforster  
> Created at: 2019-04-23 08:51:46 UTC  
> Updated at: 2019-04-23 10:02:07 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/18#issuecomment-485711307  

I encountered the same issue on boost 1.69.   
Changing the `add` function to   
```  
    template <class Pointer>  
    Pointer add(Pointer p, difference_type n) const {  
        return p + static_cast<difference_type>(n < (m_end - p) ? n : n - capacity());  
    }  
```  
or   
```  
    template <class Pointer>  
    Pointer add(Pointer p, difference_type n) const {  
        n = n < (m_end - p) ? n : n - capacity();  
        return p + n;  
    }  
```  
removed the UBSAN report for me.  
Clang version is 7.0.0   
  
Edit:  
Similarly UBSAN complained in `sub` for me as well.   
```  
runtime error: subtraction of unsigned offset from 0x6160000a5380 overflowed to 0x6160000a5448  
```  
The same fix above can be applied to `sub()` as well.

---

## Comment 3

> Username: glenfe  
> Created at: 2019-04-23 13:34:33 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/18#issuecomment-485805039  

Resolved in 03a5c849.
