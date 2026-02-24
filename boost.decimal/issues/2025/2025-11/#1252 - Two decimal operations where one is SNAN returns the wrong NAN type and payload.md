# #1252 - Two decimal operations where one is SNAN returns the wrong NAN type and payload [Closed]

> Username: mborland  
> Created at: 2025-11-19 12:41:41 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-19 18:07:40 UTC  
> Url: https://github.com/boostorg/decimal/issues/1252  

See IEEE 754 Section 7.2. If one operated is an SNAN it should be converted into a QNAN with the same payload (if applicable)
