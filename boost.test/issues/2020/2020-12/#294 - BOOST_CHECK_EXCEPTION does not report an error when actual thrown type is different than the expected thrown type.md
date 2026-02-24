# #294 - BOOST_CHECK_EXCEPTION does not report an error when actual thrown type is different than the expected thrown type [Open]

> Username: ekraihan  
> Created at: 2020-12-18 18:27:52 UTC  
> Updated at: 2020-12-18 18:33:07 UTC  
> Url: https://github.com/boostorg/test/issues/294  

Example code:  
  
```  
    BOOST_CHECK_EXCEPTION(  
        throw std::logic_error("A Logic Error"),  
        std::runtime_error,  
        [] (const std::runtime_error& e) -> bool { return true; }  
    );  
```  
I would expect this to report that the wrong exception was thrown. Indeed, the documentation for this macro seems to indicate that a report on the wrong exception is desired: https://www.boost.org/doc/libs/1_35_0/libs/test/doc/components/test_tools/reference/BOOST_CHECK_EXCEPTION.html  
  
That link states that  
  
> In case if exception does not comply to specified predicate the tool produces an error message in a form "error in <test case name>: incorrect exception <exception> is caught.  
  
Instead of an error report, the logic_error is not caught at all and bubbles up to the test suite engine. This seems to be because of the implementation of BOOST_CHECK_THROW_IMPL here: https://github.com/boostorg/test/blob/develop/include/boost/test/tools/interface.hpp#L174. That implementation only catches the *expected* type instead of all types to check if the correct type was caught.
