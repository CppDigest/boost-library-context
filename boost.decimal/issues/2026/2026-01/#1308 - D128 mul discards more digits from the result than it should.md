# #1308 - D128 mul discards more digits from the result than it should [Closed]

> Username: mborland  
> Created at: 2026-01-21 19:53:45 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-22 17:54:03 UTC  
> Url: https://github.com/boostorg/decimal/issues/1308  

We divide the u256 result in such a way to result in 34 digits. We can actually hold std::numeric_limits<u128>::digits10 to help with the correctness of rounding in the constructor
