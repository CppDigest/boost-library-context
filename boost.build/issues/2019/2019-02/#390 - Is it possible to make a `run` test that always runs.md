# #390 - Is it possible to make a `run` test that always runs? [Closed]

> Username: pdimov  
> Created at: 2019-02-03 17:05:42 UTC  
> Updated at: 2019-04-01 18:33:05 UTC  
> Closed at: 2019-04-01 18:33:05 UTC  
> Url: https://github.com/boostorg/build/issues/390  

To take `run something.cpp ; explicit something ;` for example, we want `b2 something` to run the test even if a previous such invocation already ran it.  
  
`alias always-run ; always always-run ; run something.cpp always-run ;` doesn't work.  
  
`always [ run something.cpp ] ;` doesn't work either.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-02-03 18:40:56 UTC  
> Url: https://github.com/boostorg/build/issues/390#issuecomment-460076728  

AMDG  
  
On 2/3/19 10:05 AM, Peter Dimov wrote:  
> To take `run something.cpp ; explicit something ;` for example, we want `b2 something` to run the test even if a previous such invocation already ran it.  
>   
> `alias always-run ; always always-run ; run something.cpp always-run ;` doesn't work.  
>   
  
This doesn't work, because an empty alias has nothing to build.  
  
> `always [ run something.cpp ] ;` doesn't work either.  
>   
  
It's a bug.  
  
In Christ,  
Steven Watanabe
