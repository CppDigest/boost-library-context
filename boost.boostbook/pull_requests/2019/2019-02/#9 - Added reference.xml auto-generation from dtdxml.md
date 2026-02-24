# #9  Added reference.xml auto-generation from dtdxml [Merged]

> Username: Kojoley  
> Created at: 2019-02-17 22:40:49 UTC  
> Updated at: 2024-05-14 20:19:06 UTC  
> Merged at: 2024-05-13 15:11:56 UTC  
> Closed at: 2024-05-13 15:11:56 UTC  
> Url: https://github.com/boostorg/boostbook/pull/9  

This should have been an established process, but for some reason it was  
edited manually after initial generation.  
  
After the change `reference.xml` can be removed from the repository, but  
I left it to show that the things are working fine and the difference from  
an auto-generated one are simple divergences due to manual changing  
(not updated `reference.xml` with added `<purpose>` elements in 38017203f69cec3aa5f402fe8306eb1deff67b9a). Also removed the date numbers  
from `dtdxml` as they were never in `xml` and seems to have no actual use,  
but brings a lot of noise to `xml` differences after added auto-generation.

---
