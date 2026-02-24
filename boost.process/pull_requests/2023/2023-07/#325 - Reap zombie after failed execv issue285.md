# #325 Reap zombie after failed execv issue285 [Closed]

> Username: SilverPlate3  
> Created at: 2023-07-02 19:08:22 UTC  
> Updated at: 2023-08-21 05:29:02 UTC  
> Closed at: 2023-08-21 05:29:02 UTC  
> Url: https://github.com/boostorg/process/pull/325  

**Fixing issue** - https://github.com/boostorg/process/issues/285  
  
**What was the problem**: After a failed execve, the caller was suppose to call waitpid and reap the zombie that the fork created.  
However an exception was thrown before waitpid was called.   
This caused the child fork to become a zombie.   
  
Running the following code:  
![image](https://github.com/boostorg/process/assets/93097769/477613e3-41b1-4c45-953f-1cfdaf4f18c2)  
Would result:  
![image](https://github.com/boostorg/process/assets/93097769/b91936b2-49d5-40f7-8c61-43ed340f0fd0)  
  
**What I did**:  
1) Extracting set_error() out of _read_error(). set_error() throws the exception, and is a big side effect for a function with the name of _read_error().   
2) Calling set_error() Only after the waitpid.   
3) Attempting to call waitpid() twice, with a sleep between (if needed). The first waitpid may suffer a race condition, as the child fork didn't exit yet.   
4) Unit test.

---

## Comment 1

> Username: SilverPlate3  
> Created_at: 2023-07-08 10:22:40 UTC  
> Url: https://github.com/boostorg/process/pull/325#issuecomment-1627069716  

@klemens-morgenstern   
When will this fix be integrated?   
I think it will be nice if merged before next Boost release.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2023-08-14 09:51:14 UTC  
> Url: https://github.com/boostorg/process/pull/325#issuecomment-1677023425  

I don't think this fix does it the right way tbh.

---

## Comment 3

> Username: SilverPlate3  
> Created_at: 2023-08-14 19:34:01 UTC  
> Url: https://github.com/boostorg/process/pull/325#issuecomment-1677944011  

@klemens-morgenstern   
Can you please provide me with more information for why "this fix does it the right way"? Or a code review?  
I would love to do it the right way! So please let me know what is right way in your eyes

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-08-15 03:46:39 UTC  
> Url: https://github.com/boostorg/process/pull/325#issuecomment-1678387428  

because a random sleep in a loop is the wrong choice.

---
