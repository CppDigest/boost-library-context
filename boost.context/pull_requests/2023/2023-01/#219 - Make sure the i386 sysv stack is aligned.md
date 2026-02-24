# #219 Make sure the i386 sysv stack is aligned [Merged]

> Username: ndossche  
> Created at: 2023-01-27 22:38:29 UTC  
> Updated at: 2023-01-28 07:54:15 UTC  
> Merged at: 2023-01-28 07:54:15 UTC  
> Closed at: 2023-01-28 07:54:15 UTC  
> Url: https://github.com/boostorg/context/pull/219  

The previous fix #218 had a typo in which the wrong offset was used which meant that the alignment still wasn't correct. Fix this properly now.  
  
Sorry for this mistake, my testing didn't catch this because I set it up wrongly. This PR properly fixes the alignment and fixes the bug the original PR was meant to fix.

---
