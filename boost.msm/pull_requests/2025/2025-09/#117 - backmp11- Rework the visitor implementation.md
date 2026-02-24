# #117 backmp11: Rework the visitor implementation [Merged]

> Username: chandryan  
> Created at: 2025-09-28 12:23:25 UTC  
> Updated at: 2025-09-28 14:32:53 UTC  
> Merged at: 2025-09-28 14:32:14 UTC  
> Closed at: 2025-09-28 14:32:14 UTC  
> Url: https://github.com/boostorg/msm/pull/117  

Reworked the visitor implementation in backmp11 and some other details in the same area.  
  
The new version uses perfecgt forwarding and a dispatch table mechanism, similar to the dispatch table for event processing.  
This removes the need to initialize a `m_visitors` member at runtime.  
  
More information about resolved limitations and bugs is mentioned in the README.md under backmp11's include dir.

---
