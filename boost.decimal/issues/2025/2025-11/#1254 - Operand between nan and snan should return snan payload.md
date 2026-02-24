# #1254 - Operand between nan and snan should return snan payload [Closed]

> Username: mborland  
> Created at: 2025-11-20 08:26:53 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-20 12:51:17 UTC  
> Url: https://github.com/boostorg/decimal/issues/1254  

If we have snan snan then we return the payload of the first snan not the second.
