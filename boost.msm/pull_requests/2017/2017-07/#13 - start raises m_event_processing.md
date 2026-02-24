# #13 start raises m_event_processing [Closed]

> Username: jpiw  
> Created at: 2017-07-23 17:55:18 UTC  
> Updated at: 2024-01-24 13:53:31 UTC  
> Closed at: 2024-01-24 13:53:31 UTC  
> Url: https://github.com/boostorg/msm/pull/13  

Fix for the issue #13132: process_event called upon initial on_entry does not wait for on_entry completion.

---

## Comment 1

> Username: henry-ch  
> Created_at: 2024-01-24 13:53:31 UTC  
> Url: https://github.com/boostorg/msm/pull/13#issuecomment-1908168018  

The code is now different and the fix won't work. But even implementing it will break existing code (Anonymous test). I am anyway unsure this is desired behavior.

---
