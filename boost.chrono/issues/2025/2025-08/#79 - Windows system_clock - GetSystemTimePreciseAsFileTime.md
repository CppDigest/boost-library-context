# #79 - Windows system_clock - GetSystemTimePreciseAsFileTime [Open]

> Username: garethsb  
> Created at: 2025-08-20 10:56:31 UTC  
> Updated at: 2025-08-20 10:56:31 UTC  
> Url: https://github.com/boostorg/chrono/issues/79  

Windows system_clock currently uses GetSystemTimeAsFileTime. GetSystemTimePreciseAsFileTime would provide more frequent ticks (sub-millisecond). Would it be possible to change the implementation?  
  
Compare https://github.com/rust-lang/rust/issues/67266.
