# #1032 Return false when initial check in convert_from_string fails [Closed]

> Username: SiliconA-Z  
> Created at: 2023-10-02 03:02:54 UTC  
> Updated at: 2023-10-17 19:29:35 UTC  
> Closed at: 2023-10-17 19:29:33 UTC  
> Url: https://github.com/boostorg/math/pull/1032  

Otherwise, we currently have undefined behavior, as this method returns a bool, so we cannot just return without specifying what we are returning.

---
