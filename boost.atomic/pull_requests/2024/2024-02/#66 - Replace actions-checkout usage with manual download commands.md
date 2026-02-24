# #66 Replace actions/checkout usage with manual download commands [Merged]

> Username: Lastique  
> Created at: 2024-02-05 14:56:20 UTC  
> Updated at: 2024-02-05 16:10:17 UTC  
> Merged at: 2024-02-05 16:10:00 UTC  
> Closed at: 2024-02-05 16:10:00 UTC  
> Url: https://github.com/boostorg/atomic/pull/66  

This fixes the deprecation warnings for actions/checkout@v3. actions/checkout@v4 is not functional because of the upstream bug:  
  
https://github.com/actions/checkout/issues/1590

---
