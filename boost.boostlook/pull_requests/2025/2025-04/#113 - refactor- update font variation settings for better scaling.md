# #113 refactor: update font variation settings for better scaling [Merged]

> Username: julioest  
> Created at: 2025-04-24 23:21:40 UTC  
> Updated at: 2025-04-24 23:21:51 UTC  
> Merged at: 2025-04-24 23:21:51 UTC  
> Closed at: 2025-04-24 23:21:51 UTC  
> Url: https://github.com/boostorg/boostlook/pull/113  

Updates font variation settings and stretch properties across Noto Sans fonts:   
- Changes font-stretch from semi-condensed to 62.5% 100% range  
- Updates font-variation-settings wdth from 90 to 62.5  
- Adds consistent font-stretch ranges to all font declarations  
- Sets base container font-variation-settings to wght 400, wdth 80

---
