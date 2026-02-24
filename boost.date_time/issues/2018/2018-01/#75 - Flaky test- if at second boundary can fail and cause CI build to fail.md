# #75 - Flaky test: if at second boundary can fail and cause CI build to fail [Open]

> Username: jeking3  
> Created at: 2018-01-20 06:12:10 UTC  
> Updated at: 2018-01-20 06:12:10 UTC  
> Url: https://github.com/boostorg/date_time/issues/75  

https://travis-ci.org/boostorg/date_time/jobs/330925380#L1373  
  
testing.capture-output ../../bin.v2/libs/date_time/test/testmicrosec_time_clock.test/gcc-7/debug/cxxstd-03-iso/testmicrosec_time_clock.run  
  
====== BEGIN OUTPUT ======  
2018-Jan-20 01:55:59  
2018-Jan-20 01:55:59.911262000  
Pass :: hours match   
Pass :: minutes match   
Pass :: seconds match   
Pass :: hours date   
...failed testing.capture-output ../../bin.v2/libs/date_time/test/testmicrosec_time_clock.test/gcc-7/debug/cxxstd-03-iso/testmicrosec_time_clock.run...  
  
Pass :: last is less   
2018-Jan-20 01:55:59  
2018-Jan-20 01:56:00.003424000  
Pass :: hours match   
FAIL :: minutes match   
FAIL :: seconds match   
Pass :: hours date   
Pass :: last is less   
2018-Jan-20 01:56:00  
2018-Jan-20 01:56:00.109788000  
Pass :: hours match   
Pass :: minutes match   
Pass :: seconds match   
Pass :: hours date   
Pass :: last is less
