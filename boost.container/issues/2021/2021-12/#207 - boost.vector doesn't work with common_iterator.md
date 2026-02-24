# #207 - boost.vector doesn't work with common_iterator. [Closed]

> Username: nanoric  
> Created at: 2021-12-24 14:36:04 UTC  
> Updated at: 2022-01-17 01:38:37 UTC  
> Closed at: 2022-01-12 00:38:10 UTC  
> Url: https://github.com/boostorg/container/issues/207  

Check the code blow:  
```cpp  
#include <type_traits>  
#include <boost/container/vector.hpp>  
#include <iterator>  
#include <vector>  
#include <ranges>  
  
int main(){  
    using Ints = std::vector<int>;  
    Ints ints = {1,2,3};  
  
    using I = std::counted_iterator<Ints::iterator>;  
    using S = std::default_sentinel_t;  
    using C = std::common_iterator<I, S>;  
      
#define ARGS C(std::counted_iterator(ints.begin(), ints.size()-1)), \  
          C(std::default_sentinel)  
    std::vector(ARGS);  
    // these should work, but not because of different errors.  
    //boost::container::vector<int>(ARGS);  
    //boost::container::vector(ARGS);  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-12-26 16:08:11 UTC  
> Url: https://github.com/boostorg/container/issues/207#issuecomment-1001205852  

Thanks for the report. Which compiler and version are you using?

---

## Comment 2

> Username: nanoric  
> Created at: 2021-12-26 16:58:36 UTC  
> Url: https://github.com/boostorg/container/issues/207#issuecomment-1001212665  

I'm using Visual Studio 2022 17.0.4.  
And maybe you can check with online compiler: [godbolt](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIM6SuADJ4DJgAcj4ARpjE/lykAA6oCoRODB7evv5JKWkCIWGRLDFxZgl2mA7pQgRMxASZPn4VmPaOArX1BIUR0bF6CnUNTdkVwz2hfSUDXACUtqhexMjsHOYAzKHI3lgA1CYbbgQAnomYAPoExEyECofYJhoAgpvbu5gHR1GoKQQA9GhBLcwsR/gA3KpEYgAOgQiUSDyerzMWwYOy8%2B0ObkIsSY0KRLze6I%2BXzckIcJEJKLRGKxRxuRkw9w2jyJL1CBD2LBBEDmJgA7FYXntRXsvKkjHsAJKCBRfAAieyG6BAIApBKOnKRG2FzzFMrle058sOSsFFgSAQ2goVhz1yINEtCwBliuVBFVKCWgkw6AuuJumrcsoICjVgfxVNZ9sdYudUqE7pVaqwVCYXloBAuSkEU1oV1jIvjktdbmTnrVaBYLAEAYIeOD0tIeyEOr1BuRmzTUz2zwASgBxJNuCAp71eX3%2ByPQiAmmExYChPkt%2BepABemD5AFp5gsDgBWNxxg0G0fjtMZrM5xiOMK0fnF0XjjUkCAD4f83Unvb/f57AgEGZT4FAQJZaHQPYAHcSAAaxbKIvC5BhUC5GJRAlT5UCoPZ8CoKhYlvPZYmIEgFBhH8/x%2BP4q1kEEBnVKFoxxQQHnfIchC/DsxSo34hlo4EpjiRjKWIdjPyLV4BQVDgFloTgD14PwOC0UhUE4Y9LGsZUlhWT5Nh4UgCE0WSFlgkANi4GEuAADgFMx7IANgPAUAE4NlRDYNn0ThJCUky1M4Xhww0IyTIWOBYBgRBvRYRI6FichKGreL6DicFkARC5wS4VyLgMBshguVRHOkGgs1icMICiAKolCeoTk4QzqzYQQAHkGFoRqVN4LAeSMcQetIfBiChPBIXDIbMFUKokLWVTOTaALaDwKIbmIE4PCwJreGuPAWB2hYqAMYAFAANTwTAoLa85lMM/hBBEMR2CkGRBEUFR1CG3QEgMIwUGsax9FW8NYGYNgQGooZSEhOJuAFGzTLUxIOgYSb/hTUwtMsArmQIYK2jG5wIFcUY/AFBJgimYpSgkULklSVGyZACnckZ9Jehp2ZQsqapOgmZnWd51GugaTn%2BjiLgeYFzxmhZ8ZunFmZJY0BYFF01Y9GuTA1h4OSFP8ob1I4ErJG5BQMr2HLXJhXGhj2CBNKsSwW1wQgSAOVEEj2Dw4oS4hPcsuZdvCsyQAPUL5I4PzSAOiPSGU1TjeCkBQuMnqIuiiAkCWAhEiQpKIBS/29D9d3Jbe4RRHEV6HvkJQ1ACqCbkSQ6fI4RSE4C422qQ/OuWwvZTfNy3rdt/E8Yd33UtiQP5hDjOFiApgsDiFco5juPQsT3hk9sVOwsX0hzMsmEbI0MxJEkRzHK4BHHJsmzHIFduNkNpOgsPrRM/gSKYuLtKhcAEDFOswRIoFUJ8DoA2YgVUapDTqswDaO1SAtVvB1LqAU%2BqGGAINVSI0xoTQCtNWaDYAqLSjqpFaa0GpbV1rtYg%2B027HSYKdC6V0bqMBQXXJ6NcyqyA%2Bo3b6OQ/rGEBi7ahoMoDg3YFDAgMMBjw0RnMBYqAUbpEmtuFUZosbOwsGYJgBN2jpBcAwdwstshmFClTIoEsJAJAZvkDIFi/BWLZk4pWtMuCtGMfzbozM3HCxqBMTxswFYjBcf4aWitqZ2O8WrDWL0jKjXofrDu79d6cFUE/bcpU9jAGQMgB21xJywTmFPcRNg9hu2hHPFs09/aBzMMHL%2BSMT4bBhF5Lp3Sent03uHbe3dP4hVaT/WAOc%2B4FwoEXVAfs0o5DLtCPQPDq4vX4e9BuX1VLNyYK3bgsl26dx3oFDgvc85IT2IPbJjlclmwKUUiAJSGBlKnrMmeAdNjNIXt/JemAV4DHXr5XgW8u5G2GQfdOPzj4WQFDCDQUszDn3spIXKdlXKvwySc75SMo5mExXvSFSNYapGcJIIAA%3D%3D)

---

## Comment 3

> Username: igaztanaga  
> Created at: 2022-01-12 00:34:12 UTC  
> Url: https://github.com/boostorg/container/issues/207#issuecomment-1010508586  

Thanks for the report. Boost.Container uses a simplified iterator_traits model to avoid costly include overhead, but C++20's new requirements for iterator_traits seem too complicated, so it's better to switch to std in those cases. I've committed a change (https://github.com/boostorg/move/commit/208df9f3c338674697614c125b4d98d9cc446173) in Boost.Move, which serves this iterator_traits emulation to Boost.Move and Boost.Intrusive and I'll complete it with a commit in Boost.Container.

---

## Comment 4

> Username: nanoric  
> Created at: 2022-01-17 01:38:37 UTC  
> Url: https://github.com/boostorg/container/issues/207#issuecomment-1014059885  

nice description and nice work.
