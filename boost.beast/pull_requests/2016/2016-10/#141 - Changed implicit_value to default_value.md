# #141 Changed implicit_value to default_value [Closed]

> Username: e-fominov  
> Created at: 2016-10-15 17:56:19 UTC  
> Updated at: 2016-10-17 17:34:28 UTC  
> Closed at: 2016-10-17 17:34:27 UTC  
> Url: https://github.com/boostorg/beast/pull/141  

This PR changes the usage of boost::program_options in http_server example producing simplier code  
  
Here is an explanation of the difference: http://stackoverflow.com/questions/2829806/what-is-the-difference-between-default-value-and-implicit-value-in-boostprogra

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-10-15 18:12:24 UTC  
> Url: https://github.com/boostorg/beast/pull/141#issuecomment-254001043  

[![Coverage Status](https://coveralls.io/builds/8353722/badge)](https://coveralls.io/builds/8353722)  
  
Coverage remained the same at 97.556% when pulling **be3503dc989026ac46475855afbf287e36518d63 on e-fominov:example_default_value** into **01f939d68e2f6c21d41bfe5b787fbf586cdfc064 on vinniefalco:master**.

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-10-15 18:12:28 UTC  
> Url: https://github.com/boostorg/beast/pull/141#issuecomment-254001058  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/141?src=pr) is 97.55% (diff: 100%)  
  
> Merging [#141](https://codecov.io/gh/vinniefalco/Beast/pull/141?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
``` diff  
@@             master       #141   diff @@  
==========================================  
  Files            72         72            
  Lines          4091       4091            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           3991       3991            
  Misses          100        100            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [01f939d...be3503d](https://codecov.io/gh/vinniefalco/Beast/compare/01f939d68e2f6c21d41bfe5b787fbf586cdfc064...be3503dc989026ac46475855afbf287e36518d63?src=pr)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-10-17 10:05:26 UTC  
> Url: https://github.com/boostorg/beast/pull/141#issuecomment-254166230  

Very nice! I don't know program options that well.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2016-10-17 17:34:27 UTC  
> Url: https://github.com/boostorg/beast/pull/141#issuecomment-254277094  

This is part of #146

---
