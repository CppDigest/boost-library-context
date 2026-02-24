# #222 - report_assertion() generates unreachable code warning [Closed]

> Username: k15tfu  
> Created at: 2019-05-07 17:25:23 UTC  
> Updated at: 2019-05-07 17:27:34 UTC  
> Closed at: 2019-05-07 17:27:06 UTC  
> Url: https://github.com/boostorg/test/issues/222  

Hi! Visual Studio generates C4702 in this place:  
```  
    switch( tl ) {  
    case PASS:  
        framework::assertion_result( AR_PASSED );  
        return true;  
  
    case WARN:  
        framework::assertion_result( AR_TRIGGERED );  
        return false;  
  
    case CHECK:  
        framework::assertion_result( AR_FAILED );  
        return false;  
  
    case REQUIRE:  
        framework::assertion_result( AR_FAILED );  
        framework::test_unit_aborted( framework::current_test_unit() );  
        BOOST_TEST_I_THROW( execution_aborted() );  
        return false;  <-- here  
    }  
```

---

## Comment 1

> Username: k15tfu  
> Created at: 2019-05-07 17:27:06 UTC  
> Updated at: 2019-05-07 17:27:34 UTC  
> Url: https://github.com/boostorg/test/issues/222#issuecomment-490173687  

Just noticied it was fixed in boost-1.70.0 : https://github.com/boostorg/test/issues/180
