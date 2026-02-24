# #235 - Computing "day difference" for 2 different dates returns the same number of days [Closed]

> Username: brach  
> Created at: 2023-09-11 09:18:24 UTC  
> Updated at: 2023-09-11 22:03:05 UTC  
> Closed at: 2023-09-11 22:03:05 UTC  
> Url: https://github.com/boostorg/date_time/issues/235  

I have code that uses boost datetime to calculate the number of days between a given date and December 30, 1899. The code snippet is similar to the following:  
  
auto firstDate = boost::gregorian::date firstDate(1899, 12, 30);  
auto sc_days = (chicago_ldt.date() - firstDate).days();  
  
chicago_ldt is a datetime that has been converted from UTC to America/Chicago.  
  
This calc is giving the same result for two different dates: July 24, 2022 and July 25, 2022. The calc seems to be returning 44767 for both dates. There are other consecutive dates showing the same problem.  
  
The specific timestamps used were:  
2022-Jul-24 19:21:51.670  
2022-Jul-25 07:26:03.180  
  
I'm using version 1.82.0  
  
I think the result for July 24 should be 44766.

---

## Comment 1

> Username: brach  
> Created at: 2023-09-11 22:03:05 UTC  
> Url: https://github.com/boostorg/date_time/issues/235#issuecomment-1714646419  

I think there might be something else going on in my code. Will follow-up if this still seems to be an issue.
