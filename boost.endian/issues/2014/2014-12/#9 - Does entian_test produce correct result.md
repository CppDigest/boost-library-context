# #9 - Does entian_test produce correct result? [Closed]

> Username: yurymik  
> Created at: 2014-12-05 17:35:15 UTC  
> Updated at: 2014-12-06 21:24:53 UTC  
> Closed at: 2014-12-06 21:24:53 UTC  
> Url: https://github.com/boostorg/endian/issues/9  

I might be missing something, but why detect_order() in test/endian_test.cpp does this:  
  
`View v = { 0x0102030405060708LL };`  
and then  
`memcmp( v.c, "\10\7\6\5\4\3\2\1", 8)`  
  
Shouldn't the string be `"\8\7\6\5\4\3\2\1"`?

---

## Comment 1

> Username: Beman  
> Created at: 2014-12-06 21:24:53 UTC  
> Url: https://github.com/boostorg/endian/issues/9#issuecomment-65914566  

The code is correct as written since "\10" is an octal literal. It would be clearer in hex, so I'll change it (in two places) to "\x8".  
  
Thanks,  
  
--Beman
