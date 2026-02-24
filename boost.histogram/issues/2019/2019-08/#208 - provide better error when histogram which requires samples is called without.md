# #208 - provide better error when histogram which requires samples is called without [Closed]

> Username: HDembinski  
> Created at: 2019-08-25 18:13:56 UTC  
> Updated at: 2019-12-19 12:50:39 UTC  
> Closed at: 2019-12-19 12:50:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/208  

the histogram storage requires the sample argument, the calls to operator() and fill need to disabled if the argument is not given.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-12-19 12:50:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/208#issuecomment-567476821  

was fixed
