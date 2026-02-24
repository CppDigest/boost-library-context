# #185 - Clarify behavior when you shrink using values that do not match bin edges [Closed]

> Username: HDembinski  
> Created at: 2019-04-26 08:26:00 UTC  
> Updated at: 2019-10-19 18:10:13 UTC  
> Closed at: 2019-10-19 18:10:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/185  

The current behaviour is not documented and needs to be updated. A shrink should always be conservative:  
* on the low end, it should kep the bin in which the lower value falls  
* on the high end, it should keep the bin in which the upper value falls

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-10-19 18:10:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/185#issuecomment-544182489  

closed in develop
