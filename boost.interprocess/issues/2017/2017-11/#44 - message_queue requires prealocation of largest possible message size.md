# #44 - message_queue requires prealocation of largest possible message size [Closed]

> Username: feldi12  
> Created at: 2017-11-14 16:51:37 UTC  
> Updated at: 2017-12-08 11:51:58 UTC  
> Closed at: 2017-12-08 11:51:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/44  

Hi!  
In current implementation message_queue checks supplied buffer size against maximum possible message size, not against the current message size.  
Moving check to place, where size of next object is already known would allow user to increase the allocated size only when needed. This would help limit memory allocation in case of queue sending messages of very different sizes.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-12-08 11:51:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/44#issuecomment-350245657  

message_queue is modelled after POSIX message queues, where configuration parameters are:  
  
long    mq_maxmsg   maximum number of messages  
long    mq_msgsize  maximum message size  
  
According to this POSIX model (http://pubs.opengroup.org/onlinepubs/009695399/functions/mq_send.html):  
  
_"The value of msg_len shall be less than or equal to the mq_msgsize attribute of the message queue, or mq_send() shall fail."_  
  
Many users depend on this design so it's not feasible to change it. Anyway, many thanks for the report.
