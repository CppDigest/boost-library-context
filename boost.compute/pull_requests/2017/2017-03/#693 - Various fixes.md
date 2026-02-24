# #693 Various fixes [Merged]

> Username: jszuppe  
> Created at: 2017-03-17 20:59:59 UTC  
> Updated at: 2017-03-21 03:07:54 UTC  
> Merged at: 2017-03-21 03:07:52 UTC  
> Closed at: 2017-03-21 03:07:52 UTC  
> Url: https://github.com/boostorg/compute/pull/693  

* Fixes https://github.com/boostorg/compute/issues/683 - variable type is kept when using it in `boost::compute::lambda`. I decided that for now it's better not to change `operator<<` operators for `detail::meta_kernel` as was proposed in https://github.com/boostorg/compute/pull/684. Instead, I specialised `void operator()(proto::tag::terminal, const T &x)` in `lambda::context`.  
* Remove race conditions from test in `test_async_wait_guard.cpp` and from test `destructor_callback` in `test_buffer.hpp`

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-03-17 22:13:51 UTC  
> Url: https://github.com/boostorg/compute/pull/693#issuecomment-287484497  

[![Coverage Status](https://coveralls.io/builds/10654674/badge)](https://coveralls.io/builds/10654674)  
  
Coverage decreased (-0.04%) to 83.314% when pulling **d0f3e50a32ab404c58e6f81f1f2ac78b28d647db on haahh:pr_fix** into **cc30762666aa9b20f62751cdf119f2cf1a103532 on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2017-03-21 03:07:54 UTC  
> Url: https://github.com/boostorg/compute/pull/693#issuecomment-287963803  

Awesome! Thanks for fixing these!

---
