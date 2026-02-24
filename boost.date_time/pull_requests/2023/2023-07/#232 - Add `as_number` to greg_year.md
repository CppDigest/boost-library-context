# #232 Add `as_number` to greg_year [Open]

> Username: ecatmur  
> Created at: 2023-07-25 16:33:15 UTC  
> Updated at: 2023-07-25 16:33:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/232  

for consistency with `greg_day`, `greg_month`.  
  
This is useful e.g. for `std::format("{:02}/{:02}/{:04}", month.as_number(), day.as_number(), year.as_number())`.

---
