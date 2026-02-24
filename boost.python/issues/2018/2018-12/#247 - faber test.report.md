# #247 - faber test.report [Open]

> Username: iris0329  
> Created at: 2018-12-28 12:54:29 UTC  
> Updated at: 2018-12-29 01:12:08 UTC  
> Url: https://github.com/boostorg/python/issues/247  

when I run the `faber test.report` , it shows  
  
```  
configuration check results:  
  has_cxx11 : False (cached)  
  has_numpy : True (cached)  
gxx.link src.boost_python27  
gxx.makedep test.injected.d  
gxx.compile test.injected.o  
gxx.link test.injected_ext  
python.run test.injected  
running...  
Done.  
main() should return report_errors()  
Aborted (core dumped)  
  
test.injected: FAIL  
gxx.makedep test.properties.d  
gxx.compile test.properties.o  
gxx.link test.properties_ext  
python.run test.properties  
running...  
  
```  
g++: 4.8.5  
boost: 1.69.0  
  
----------------------------  
it shows  `has_cxx11 : False (cached)`, I wonder is it ok to have this False?  
although some tests shows PASS   
thanks for help！

---

## Comment 1

> Username: iris0329  
> Created at: 2018-12-29 01:12:08 UTC  
> Url: https://github.com/boostorg/python/issues/247#issuecomment-450451033  

the finally test result is   
```  
test summary: 75 passes, 6 failures, 4 expected failures, 4 skipped  
failures:  
test.injected  
test.boost_shared_ptr  
test.operators  
test.implicit  
test.data_members  
test.extract  
  
...failed updating 11 artefacts...  
...made 419 artefacts...  
  
```
