# #280 - figure out how range deals with error::end and other errors [Closed]

> Username: vinniefalco  
> Created at: 2022-07-31 03:18:39 UTC  
> Updated at: 2022-08-02 02:06:50 UTC  
> Closed at: 2022-08-02 02:06:50 UTC  
> Url: https://github.com/boostorg/url/issues/280  

`range_rule` needs a specification for how it deals with ordinary parsing errors, otherwise it can only be used with rules that are designed to return `error::end`. This significantly limits its utility.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-02 02:06:49 UTC  
> Url: https://github.com/boostorg/url/issues/280#issuecomment-1201932327  

Done. `error::end` needs exposition.
