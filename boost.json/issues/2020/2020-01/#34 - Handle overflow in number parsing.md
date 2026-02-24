# #34 - Handle overflow in number parsing [Closed]

> Username: vinniefalco  
> Created at: 2020-01-13 17:41:59 UTC  
> Updated at: 2020-08-10 20:21:25 UTC  
> Closed at: 2020-08-10 20:21:25 UTC  
> Url: https://github.com/boostorg/json/issues/34  

In some areas of number parsing, e.g. `++dig_` or `++sig_` we need to check for overflow / wraparound and handle it. This will also need tests.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-10 20:21:25 UTC  
> Url: https://github.com/boostorg/json/issues/34#issuecomment-671570291  

The only variable that has potential to overflow/underflow is `bias`, and this will only happen in extreme cases (e.g. numbers > 2.4 billion digits long).
