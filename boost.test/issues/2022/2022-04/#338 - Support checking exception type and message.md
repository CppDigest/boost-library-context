# #338 - Support checking exception type and message [Open]

> Username: teeks99  
> Created at: 2022-04-11 19:23:00 UTC  
> Updated at: 2022-04-11 19:23:00 UTC  
> Url: https://github.com/boostorg/test/issues/338  

For some basic code:  
  
```C++  
  
#include <stdexcept>  
  
class MyException : public std::runtime_error  
{  
public:  
   MyException(std::string cause)  
      : std::runtime_error(cause)  
   {}  
};  
  
void MyCode()  
{  
   throw MyException("A very specific message");  
}  
```  
  
I have a pattern that is very frequently used:  
  
```C++  
BOOST_AUTO_TEST_CASE(CheckCorrectException)  
{  
   std::string expectedMessage = "A very specific message";  
     
   BOOST_CHECK_EXCEPTION(MyCode(), MyException, [expectedMessage](const MyException& ex) -> bool  
   {  
       BOOST_CHECK_EQUAL(ex.what(), expectedMessage);  
       return true;  
   });  
}  
```  
  
I was wondering if this pattern of checking they type and message could be common enough to make another check that explicitly checks the type *and* message.  
  
e.g. `BOOST_CHECK_EXCEPTION_MESSAGE(statement, exception, message)`
