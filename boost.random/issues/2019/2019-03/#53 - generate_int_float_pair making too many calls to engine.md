# #53 - generate_int_float_pair making too many calls to engine [Closed]

> Username: ghost  
> Created at: 2019-03-28 11:42:27 UTC  
> Updated at: 2019-03-28 20:23:18 UTC  
> Closed at: 2019-03-28 20:23:18 UTC  
> Url: https://github.com/boostorg/random/issues/53  

Hi!  
  
It seems to me that `generate_int_float_pair` is not working correctly. To illustrate the issue, consider the case in which `RealType` is 64-bit, `w = 8` and the engine is 32-bit, e.g. `boost::random::mt19937`.  
  
Going through the code of `generate_int_float_pair`, it starts by generating 32 random bits and sending 8 of them to `bucket`. The remaining 24 bits are put into `r`. At this point (line 85) we still need 40 random bits, so we should be calling the engine two more times. However, the for-loop on lines 85-89 is executed 8 times, before calling one last time the engine at line 93. The engine is called 10 times in this case.  
  
For these values, the naive code   
  
`std::uniform_int_distribution<int> int_dis(0, 255);`  
`auto int_result(int_dis(engine));`  
`auto float_result(std::generate_canonical<RealType, std::numeric_limits<RealType>::digits>(engine));`  
  
ends up being faster since it only calls the engine three times (once to generate the int, twice to generate the `RealType`). I have also found that this issue causes the Ziggurat normal generation method to actually be slower than the (quite slow) libstdc++ implementation.  
  
I do not feel competent enough to fix this issue (if there indeed is one) but my impression is that the bounds of the for-loop on line 85 should be adjusted, and the multiplying factor on line 88 should be changed, perhaps depending on `i`.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-03-28 14:40:19 UTC  
> Url: https://github.com/boostorg/random/issues/53#issuecomment-477623922  

AMDG  
  
The correct fix is to change ++i to i += m on line 85.  
The reason that I missed this is that the for loop  
is skipped entirely for mt19937, w=8 and float/double.  
  
In Christ,  
Steven Watanabe
