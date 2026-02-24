# #187 - Error handling in boost::posix_time::from_iso_extended_string() [Closed]

> Username: elenaepstein66  
> Created at: 2021-04-22 18:28:47 UTC  
> Updated at: 2021-05-02 15:57:37 UTC  
> Closed at: 2021-05-02 15:57:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/187  

If invalid ISO date is provided as the argument (e.g. "2021-04-21”) the exception thrown is  
basic_string::at: __n (which is 0) >= this->size() (which is 0)

---

## Comment 1

> Username: JeffGarland  
> Created at: 2021-05-02 14:27:00 UTC  
> Url: https://github.com/boostorg/date_time/issues/187#issuecomment-830817890  

I have a patch for this case that pre-checks the string size so if it's only a date the ptime will be constructed only from the date string -- so basically defaulting to <date>:00:00:00.  I added a test and confirmed that a completely broken string like 2021-1 now provides a date_time exception (derived from std::exception) instead of a string exception -- specifically in that case of 2021-1 it is "Day of month value is out of range 1..31".
