# #1197 - Review 2: charconv example [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 18:45:54 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-11 10:49:53 UTC  
> Url: https://github.com/boostorg/decimal/issues/1197  

The example on charconv uses an assert to check the return value,  
and this makes me very uncomfortable [8]. I've found real-life  
projects with people "checking" return values with assert like this  
(there are developers that haven't heard about NDEBUG, apparently). So  
I'd be more comfortable with an `if (r_from) { /* handle failure */  
}`.
