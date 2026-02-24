# #125 - gregorian calendar day_number() uses wrong epoch [Closed]

> Username: p12tic  
> Created at: 2020-03-01 13:33:03 UTC  
> Updated at: 2021-11-27 18:29:31 UTC  
> Closed at: 2021-11-27 18:29:31 UTC  
> Url: https://github.com/boostorg/date_time/issues/125  

[Documentation of day_number()](https://www.boost.org/doc/libs/1_71_0/doc/html/date_time/gregorian.html#gregcal_functions) says that:  
  
> Convert a ymd_type into a day number. The day number is an absolute number of days since the epoch start.  
  
However, `gregorian_calendar::day_number(gregorian_calendar::epoch())` is equal to 2232400. I would expect this to be zero.  
  
The same behavior carries over to the `date` type. All of the following are `true`:  
  
`date(1400, Jan, 1).day_number() == 2232400`  
`date(min_date_time).day_number() == 2232400`  
`date(gregorian_calendar::epoch()).day_number() == 2232400`  
  
It's pretty strange such an issue has been unnoticed for so many years. Is it considered a bug at all? Maybe the documentation is wrong or unclear and I'm misunderstanding it?

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-03-01 23:27:13 UTC  
> Url: https://github.com/boostorg/date_time/issues/125#issuecomment-593164422  

Well it's really a bug in the docs since clearly this isn't true.  The day number is driven by the implementation of the proleptic gregorian calendar which for boost date-time gregorian::date is 'arbitrarily' cut off at 1400-01-01.  You could generate a date type based on the algorithm that goes back further - although fortunately I don't believe there's ever been a request for that.    
  
And, of course now this could never be changed -- since someone unquestionably depends on storing this number - for anyone that reads this, please don't do that, it's fragile design.  If it were ever under consideration to change this number there would need to be consideration of efficiencies -- we don't want to add additional calculations if we don't have to.

---

## Comment 2

> Username: p12tic  
> Created at: 2021-11-27 18:29:31 UTC  
> Url: https://github.com/boostorg/date_time/issues/125#issuecomment-980781523  

Thanks for a very clear explanation, I fully agree with the assessment and sorry for not responding earlier. I think 382658a fixed the problem, so I'm closing this issue.
