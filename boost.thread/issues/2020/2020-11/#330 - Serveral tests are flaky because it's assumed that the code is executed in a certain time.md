# #330 - Serveral tests are flaky because it's assumed that the code is executed in a certain time [Open]

> Username: yuxianch  
> Created at: 2020-11-24 06:04:56 UTC  
> Updated at: 2020-11-24 06:07:21 UTC  
> Url: https://github.com/boostorg/thread/issues/330  

Serveral tests in thread/test/ are flaky(sometimes pass, sometimes fail) because it assumes that the code is executed in a certain time. When the host is low or overloadded, the test will fail.  
Take test_condition_notify_all.cpp as an example. The test assumes that thread1, thread2 and thread3 are finished after notify_all() and sleep 200ms are run. However, it's possible that the thread(s) are not finished after notify_all() and sleep 200ms. The test may need to consider such situation.  
https://github.com/boostorg/thread/blob/409c98f8b745e72bc326e93bfaf8a353d94a69b0/test/test_condition_notify_all.cpp#L180-L203
