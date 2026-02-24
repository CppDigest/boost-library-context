# #455 Results more warn finding and repair typo GMP [Merged]

> Username: ckormanyos  
> Created at: 2022-05-02 13:58:19 UTC  
> Updated at: 2022-05-03 04:23:45 UTC  
> Merged at: 2022-05-03 04:23:45 UTC  
> Closed at: 2022-05-03 04:23:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/455  

This little PR handles:  
- One hard-to-find `nullptr` warning found in another project.  
- An actual typo that had been made in my GMP _div-by-zero_ correction(s).  
  
Subtle, hard-to-find stuff. So let's patiently run CI prior to turning to the next larger batch of warns.

---
