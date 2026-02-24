# #5 - Potentially uninitialized variable boost_array "result" in make_partial_xor_products_table [Closed]

> Username: blayet  
> Created at: 2019-05-10 13:14:21 UTC  
> Updated at: 2024-04-13 17:42:22 UTC  
> Closed at: 2024-04-13 17:42:22 UTC  
> Url: https://github.com/boostorg/crc/issues/5  

Hi  
  
Boost 1.7 on Windows VS 2017 15.9.11  
warning C4701: potentially uninitialized local variable 'result' used  
  
Be good to not have to ignore this warning ...  
thanks  
ben

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-13 17:42:22 UTC  
> Url: https://github.com/boostorg/crc/issues/5#issuecomment-2053712675  

Should have been fixed by https://github.com/boostorg/crc/pull/6.
