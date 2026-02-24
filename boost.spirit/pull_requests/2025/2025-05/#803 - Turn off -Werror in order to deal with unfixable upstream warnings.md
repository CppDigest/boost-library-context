# #803 Turn off -Werror in order to deal with unfixable upstream warnings [Merged]

> Username: saki7  
> Created at: 2025-05-09 06:43:05 UTC  
> Updated at: 2025-05-09 07:22:56 UTC  
> Merged at: 2025-05-09 07:22:52 UTC  
> Closed at: 2025-05-09 07:22:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/803  

Make -Werror off, as it can't handle upstream warnings properly.  
Specifically, container and math is emitting warnings that makes spirit unmaintainable since all tests fail.  
  
Also includes some QoL fixes on the CI configuration.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-05-09 06:56:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/803#issuecomment-2865364510  

Before/after  
  
![image](https://github.com/user-attachments/assets/d334207c-246c-41d2-9127-eedc7ed100d7)  
  
![image](https://github.com/user-attachments/assets/f717f4fb-89b3-420d-9255-7ad488c44b47)

---
