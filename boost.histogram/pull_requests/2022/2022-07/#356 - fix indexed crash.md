# #356 fix indexed crash [Merged]

> Username: HDembinski  
> Created at: 2022-07-18 16:30:00 UTC  
> Updated at: 2022-07-20 07:51:17 UTC  
> Merged at: 2022-07-20 07:51:16 UTC  
> Closed at: 2022-07-20 07:51:16 UTC  
> Url: https://github.com/boostorg/histogram/pull/356  

`indexed` logic was faulty if any axis except the first had a physical size (inner bins + flow bins) of 0. Closes #353  
  
Investigating this revealed another issue: the logic was also faulty if the histogram contained axes that had a logical size of 0 (only flow bins but no inner bins). This patch fixes both issues.

---
