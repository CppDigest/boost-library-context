# #40 - boost::rational serialized as wstring returns always "1" [Open]

> Username: sriediger  
> Created at: 2019-11-03 10:45:51 UTC  
> Updated at: 2020-10-06 01:38:07 UTC  
> Url: https://github.com/boostorg/rational/issues/40  

Dear boost rational team,  
  
converting boost rational to **wstring** always returns "1" instead of the correct value  
`    const boost::rational<int> editRate(1, 3);  
    std::cout << editRate << "\n";    // -> 1/3 , OK  
    std::wcout << editRate << L"\n";  // -> 1   , Error  
`  
Tested with boost 1.71.0/1.66.0, Visual C++ 2017/Compiler Explorer GCC 9.2.  
  
https://godbolt.org/z/STtFqN  
  
![boost_rational_wstring_error](https://user-images.githubusercontent.com/57315907/68083871-76682c00-fe2e-11e9-8f15-d41f12cf5dd1.png)  
  
best regards,  
Stefan

---

## Comment 1

> Username: nyanpasu64  
> Created at: 2020-10-06 01:38:07 UTC  
> Url: https://github.com/boostorg/rational/issues/40#issuecomment-703978129  

I've experienced similar issues with other serialization methods. I think the rational is being converted to a bool, then printed as an integer.
