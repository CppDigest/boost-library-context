# #265 - Message queue receive filter predicate [Open]

> Username: yrashk  
> Created at: 2025-06-17 13:30:15 UTC  
> Updated at: 2026-01-07 08:51:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/265  

I am interested to develop and contribute message queue predicate for filtering messages.   
  
The use case is multiple consumers only some of which would be qualified/interested in consuming certain types of messages. Currently, I simulate this by requeueing the messages until it reaches the right consumer. This is obvious subefficient.  
  
I wonder if there's any pre-existing notion of such functionality – and whether it would be of interest.

---

## Comment 1

> Username: yrashk  
> Created at: 2025-07-25 04:08:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/265#issuecomment-3116319169  

@igaztanaga Hi Ion, pinging to see if there would be interest in such functionality?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2026-01-07 08:51:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/265#issuecomment-3717880725  

Reviewing old bugs. The feature seems interesting but it seems to me that it could not work with current message_queue philosophy (priority-based recepcion). Apart from the interface change, an efficient implementation probably would require a different implementation. Do you have something in mind to solve these issues?
