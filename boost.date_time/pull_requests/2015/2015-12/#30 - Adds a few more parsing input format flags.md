# #30 Adds a few more parsing input format flags [Closed]

> Username: ylow  
> Created at: 2015-12-09 05:10:40 UTC  
> Updated at: 2018-03-12 20:03:38 UTC  
> Closed at: 2018-03-12 20:03:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/30  

Adds a few more input format  
%I,%l : 12 hour  
%P: AM PM  
%p: am pm  
  
Modified %d to support single digit day.  
This allows parsing of 1-1-1984 with %d-%m-%Y  
  
If this looks acceptable I will add tests.

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-11-05 00:55:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/30#issuecomment-341941156  

This is acceptable to me but we need both tests and updated docs for the new input format characters.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-03-12 20:03:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/30#issuecomment-372444319  

This pull request can be reopened if there is interest in completing it.    
The work needs to be rebased on the current master.

---
