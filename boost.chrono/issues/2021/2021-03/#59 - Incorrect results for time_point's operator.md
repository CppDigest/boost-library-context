# #59 - Incorrect results for time_point's operator >> [Open]

> Username: rkwk  
> Created at: 2021-03-07 02:20:48 UTC  
> Updated at: 2021-03-07 02:37:32 UTC  
> Url: https://github.com/boostorg/chrono/issues/59  

In case when no year or day of month is specified in the time format, stream extraction of a `time_point` will yield incorrect results. The reason for this is that struct `std::tm` is initialised in various places to all zeroes using `memset`, which is not correct, because in this struct day of month is counted from 1, not 0, and years are counted from 1900, not 1970. Therefore, if the fields are passed unmodified to `timegm` (which is the case when the parsed timestamp has no year or day in its format), the result will be off by the relevant differences.  
  
For example, if "01" is parsed with time format "%S", the expected result would be 1970-01-01T00:00:01, but instead it's 1899-12-31T00:00:01.
