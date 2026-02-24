# #185 - group_wait.cpp is flaky [Open]

> Username: yuxianch  
> Created at: 2020-11-24 02:18:53 UTC  
> Updated at: 2021-05-24 12:07:30 UTC  
> Url: https://github.com/boostorg/process/issues/185  

group_wait.cpp in process/test is flaky(sometimes passed, sometime failed in running). When running multiple tests at the same time, the possibility of meeting failure is much higher.  
The test expects c1 and c2 is done in 5 seconds, but it's hard to satisfy this requirement when the machine is overloadded.  
https://github.com/boostorg/process/blob/1b4d67170fec7ba5523d58405a2c40f80eb1290b/test/group_wait.cpp#L34-L81  
  
The timeout of unit_test or the sleep time should be set larger or modified to avoid flaky failure.  
https://github.com/boostorg/process/blob/1b4d67170fec7ba5523d58405a2c40f80eb1290b/test/group_wait.cpp#L34  
https://github.com/boostorg/process/blob/1b4d67170fec7ba5523d58405a2c40f80eb1290b/test/group_wait.cpp#L40-L41
