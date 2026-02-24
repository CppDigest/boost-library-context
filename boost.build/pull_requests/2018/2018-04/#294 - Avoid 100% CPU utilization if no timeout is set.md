# #294 Avoid 100% CPU utilization if no timeout is set [Merged]

> Username: neuschaefer  
> Created at: 2018-04-03 15:36:05 UTC  
> Updated at: 2023-02-24 10:04:31 UTC  
> Merged at: 2018-05-13 02:27:48 UTC  
> Closed at: 2018-05-13 02:27:48 UTC  
> Url: https://github.com/boostorg/build/pull/294  

While building boost, I noticed that jam0 and bjam used 100% of a CPU.  
Strace showed that they were calling poll with a zero timeout in a loop.  
This is because:  
- the logic in exec_wait() initializes select_timeout to globs.timeout  
- globs.timeout is zero when no action timeout is specified  
- poll interprets a zero timeout as "return immediately" rather than  
  "wait indefinitely".  
  
Fix this by using a poll timeout of one minute when globs.timeout is  
zero. Note that the value of one minute is arbitrary: Other values  
should work just as well.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-04-03 15:57:40 UTC  
> Url: https://github.com/boostorg/build/pull/294#issuecomment-378301483  

AMDG  
  
On 04/03/2018 09:36 AM, Jonathan Neuschäfer wrote:  
> While building boost, I noticed that jam0 and bjam used 100% of a CPU.  
> Strace showed that they were calling poll with a zero timeout in a loop.  
> This is because:  
> - the logic in exec_wait() initializes select_timeout to globs.timeout  
> - globs.timeout is zero when no action timeout is specified  
> - poll interprets a zero timeout as "return immediately" rather than  
>   "wait indefinitely".  
>   
> Fix this by using a poll timeout of one minute when globs.timeout is  
> zero. Note that the value of one minute is arbitrary: Other values  
> should work just as well.  
  
  It's better to set the timeout to -1, which does mean  
"wait indefinitely".  This matches the original behavior  
of using select with a NULL timeout.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: neuschaefer  
> Created_at: 2018-04-03 16:02:00 UTC  
> Url: https://github.com/boostorg/build/pull/294#issuecomment-378302921  

Good idea, I'll update my patch.

---
