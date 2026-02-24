# #167 - Inexactness in julian_day() function. [Open]

> Username: Zyro9922  
> Created at: 2020-08-13 11:54:12 UTC  
> Updated at: 2020-09-03 23:52:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/167  

If we compute Julian Day Number for 13th August 2020 using julian_day() we get **2459075**.   
But actual Julian Day number is **2459074.5**.  
  
For 22nd April 1980 using julian_day() we get **2444352**.  
Actual Julian Day number is **2444351.5**.  
  
For 7th September 1999 using julian_day() we get **2451429**.  
Actual Julian Day number is **2451428.5**.  
  
There is always a difference of **0.5** in actual and what julian_day() number is returning.  
  
**Verified from**  
|  |  |  
| ------ | ------ |  
| Keisan Online Calculator | https://keisan.casio.com/exec/system/1227779487 |  
| Neoprogrammics | http://neoprogrammics.com/sidereal_time_calculator/index.php |  
  
I am working on Boost.Astronomy and Julian Day number of a day is required when calculating Greenwich Sidereal Time.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-09-03 23:51:26 UTC  
> Updated at: 2020-09-03 23:52:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/167#issuecomment-686821057  

So I don't think the issue here is inexactness -- in fact it is exact.  You'll note that in all test cases here the '.5' has been rounded up because date_time assumes the center of the gregorian day (aka noon) in it's calculation and not 00:00:00 of the day.  Also gregorian::date julian_day interface is in terms of integer days -- the 'date' type doesn't represent a resolution below a day' -- that's what ptime is for.  So it really can't represent the '.5' in the interface.  
  
I'm sympathetic to the point that the current result is 'less than ideal'.  One could reasonably expect to get 2459074 for 2020-08-13 -- but then again 2459075 is actually just as correct for 50% of that day.  At a minimum, the documentation should explain what to expect (it does not).  It also seems that what's really missing is a similar function on ptime that would provide the correct values with fractional precision.  In that case 2020-08-13 00:00:00 would given 2459074.5 and 2020-08-13 12:00:00 would give 2459075.0
