# #111 - Add possibility to cancel `timed_receive` of `message_queue_t` [Closed]

> Username: ndusart  
> Created at: 2020-04-08 13:58:07 UTC  
> Updated at: 2020-04-08 14:48:12 UTC  
> Closed at: 2020-04-08 14:48:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/111  

Hello,  
  
In one of my project, I have a program that may start a message queue and listen for messages for a long period of time.  
  
So I periodically call `timed_receive` in a loop until that listening is aborted by the user and then I need to clean some resources used by the thread running `timed_receive`.  
  
As there is no way to actually cancel the wait, we have to use very short timeouts so that the abortion of the thread may be done in a timely manner, but this quite defeats the purpose of calling `timed_receive` in the first place.  
  
This would be great if we could have a function `cancel` in `message_queue_t` that makes any pending and futures operations returns as soon as possible.  
  
I wondered maybe this could be done by adding a boolean flag in `message_queue` that would be checked when the wait on the `p_hdr->m_cond_recv` is over and the queue is still empty. If the flag is set, the `timed_*` function could return `false`, and blocking functions could throw an exception.  
  
The call to `cancel` would set the boolean flag and notify `p_hdr->m_cond_recv`.  
  
I'd be glad to help implement it if you can give me some mentoring :)

---

## Comment 1

> Username: ndusart  
> Created at: 2020-04-08 14:48:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/111#issuecomment-611002980  

As always, writing the question force us to rethink a bit about the problem.  
  
I understand the complexity that could be involved in adding this.  
And we can already just send a special message to cancel the current `timed_receive`.  
  
Sorry for having opened this issue.
