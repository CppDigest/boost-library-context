# #1306 - Fix underflow in mul with subnormal values [Closed]

> Username: mborland  
> Created at: 2026-01-20 22:05:14 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-21 22:37:21 UTC  
> Url: https://github.com/boostorg/decimal/issues/1306  

In decimal64 1.3e-394 * 1e-4 should yield 1e-398 (or 1.3e-398), but currently yields 1.3e-397. I assume this is due to the way we pack subnormals in the constructor.
