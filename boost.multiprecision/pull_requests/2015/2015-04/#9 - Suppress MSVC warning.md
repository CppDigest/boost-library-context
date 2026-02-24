# #9 Suppress MSVC warning [Merged]

> Username: MarcelRaad  
> Created at: 2015-04-02 09:40:02 UTC  
> Updated at: 2015-04-02 10:08:50 UTC  
> Merged at: 2015-04-02 10:06:14 UTC  
> Closed at: 2015-04-02 10:06:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/9  

warning C4244: 'argument': conversion from 'int' to 'char', possible loss of data  
  
This causes the Boost.Units regression tests to fail on MSVC because of warnings-as-errors:  
http://www.boost.org/development/tests/develop/developer/output/teeks99-08f-win2012R2-64on64-boost-bin-v2-libs-units-test-test_information_units-test-msvc-14-0-dbg-adrs-mdl-64-archt-x86-async-excpt-on-thrd-mlt.html

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-04-02 10:06:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/9#issuecomment-88855804  

Thanks!

---
