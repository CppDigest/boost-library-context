# #60 - Race in process::child and a bad fix [Closed]

> Username: pfaffe  
> Created at: 2018-12-14 13:17:47 UTC  
> Updated at: 2018-12-18 04:58:45 UTC  
> Closed at: 2018-12-18 04:58:45 UTC  
> Url: https://github.com/boostorg/process/issues/60  

In 1.65, there was a race when using `boost::process::child::wait_for`. Compiling the attached [example](https://github.com/boostorg/process/files/2680177/flaky_children.txt) with C++17 and gcc 7.3 on linux (although other version and clang are affected too) sometimes produces the expected output  
```  
$ ./a.out foo  
Timeout  
Finished  
```  
And sometimes immediately without waiting  
```  
$ ./a.out foo  
Exited with code 252  
Finished  
```  
which I assume is caused by a race somewhere in the fork and pipe setup part.  
  
Interestingly, the bug is gone in 1.69, but I didn't find a ticket for it so I'm still reporting. Mostly because the fix got it wrong, too. Although there is no more race, `wait_for` now sleeps for the given time. Independent of whether the child exits or not. To reproduce, change the `Sleep` timespan to like 3s in the testcase.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-12-15 01:55:33 UTC  
> Url: https://github.com/boostorg/process/issues/60#issuecomment-447527416  

Thanks for reporting, those functions really are trouble. Can you test against the current masterbrach, I just merged another fiy yesterday and my tests pass.

---

## Comment 2

> Username: pfaffe  
> Created at: 2018-12-17 21:13:14 UTC  
> Url: https://github.com/boostorg/process/issues/60#issuecomment-448000778  

With master the issue doesn't reproduce anymore for my testcase and production use case!  
  
Cheers!

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-12-18 04:58:45 UTC  
> Url: https://github.com/boostorg/process/issues/60#issuecomment-448096766  

Awesome, that stuff was a nightmare I tell you.
