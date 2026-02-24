# #31 - Bug in multiprecision shift left [Closed]

> Username: chfast  
> Created at: 2017-10-03 15:38:51 UTC  
> Updated at: 2017-12-27 14:02:27 UTC  
> Closed at: 2017-12-26 17:08:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/31  

In cpp_int this test fails:  
  
```  
using uint256 = number<cpp_int_backend<256, 256, unsigned_magnitude, unchecked, void>>;  
BOOST_CHECK_EQUAL(uint256(3) << 255, uint256(1) << 255);  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-12-26 17:08:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/31#issuecomment-353990642  

Works for me with both msvc and gcc and current develop (and last release).  I *think* I remember fixing something like this, but can't find the commit at present.... please reopen if you still have issues with Boost-1.66.

---

## Comment 2

> Username: chfast  
> Created at: 2017-12-26 19:47:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/31#issuecomment-354006134  

Thanks. I will check 1.66.

---

## Comment 3

> Username: chfast  
> Created at: 2017-12-27 14:02:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/31#issuecomment-354118184  

I confirm this is fixed in 1.65.1 (the version we are currently using).
