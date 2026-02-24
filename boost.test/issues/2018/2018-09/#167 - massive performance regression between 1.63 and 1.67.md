# #167 - massive performance regression between 1.63 and 1.67 [Closed]

> Username: mgaunard  
> Created at: 2018-09-21 14:45:57 UTC  
> Updated at: 2018-09-21 18:11:24 UTC  
> Closed at: 2018-09-21 18:11:24 UTC  
> Url: https://github.com/boostorg/test/issues/167  

Hi,  
  
I haven't been able to bisect yet but I have observed a *massive* performance degradation between 1.63 and 1.67 when using a BOOST_DATA_TEST_CASE with an xrange of 100,000 values.  
  
It takes 10 minutes just for the test system to initialize itself. It was instant before.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-09-21 18:11:18 UTC  
> Url: https://github.com/boostorg/test/issues/167#issuecomment-423625787  

Thanks for the report. I believe this is a duplicate of #163. Solved already but not merged.
