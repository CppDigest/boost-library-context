# #106 - Optional leading zero for the 'day of month' format specifier [Open]

> Username: firedaemon-cto  
> Created at: 2019-06-07 14:04:20 UTC  
> Updated at: 2025-02-03 16:27:10 UTC  
> Url: https://github.com/boostorg/date_time/issues/106  

The [documentation](https://www.boost.org/doc/libs/1_70_0/doc/html/date_time/date_time_io.html#date_time.format_flags) for the format flags of date/time input facets state  
"%d - Day of the month as decimal 01 to 31.  When used to parse input, **the leading zero is optional**".  
  
Does this also apply to the time input facet? The time input face code does something else:  
https://github.com/boostorg/date_time/blob/b0437e2999a65668dc4178dbb817a89a382ece94/include/boost/date_time/time_facet.hpp#L1096  
  
Hence, it fails to parse a date/time string of format "%m-%d-%Y %H:%M:%S", e.g. "6-5-2019 21:06:15".  
  
Minimal example code:  
  
    wstring s = L"6-5-2019 21:06:15";  
  
    wistringstream in{ s };  
    in.imbue({ locale::classic(), new wtime_input_facet{L"%m-%d-%Y %H:%M:%S"} });  
  
    ptime pt;  
    in >> pt;  
    assert(!pt.is_not_a_date_time());

---

## Comment 1

> Username: petebannister  
> Created at: 2025-02-03 16:27:09 UTC  
> Url: https://github.com/boostorg/date_time/issues/106#issuecomment-2631483043  

This is also a problem for time components e.g. minutes / seconds.  and it differs from strptime or std::get_time where leading zero is optional.  You would want it to be somewhat greedy though if e.g. parsing somthing like an ISO date time such as 20250202T120203.  
  
One would intuitively expect a format string of "%s" to be able to parse the string "0" or "0.1".  But it fails (in boost 1.81 at least - I didn't see any relevant updates in the change log).
