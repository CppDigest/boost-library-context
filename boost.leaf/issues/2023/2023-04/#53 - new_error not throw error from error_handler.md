# #53 - new_error not throw error from error_handler [Closed]

> Username: in-text  
> Created at: 2023-04-04 05:45:14 UTC  
> Updated at: 2023-04-09 19:32:02 UTC  
> Closed at: 2023-04-05 04:31:34 UTC  
> Url: https://github.com/boostorg/leaf/issues/53  

Hello! I've try to accept some error, and throw new, but in the next error handler i've not accept new error.  
```  
#include <iostream>  
#include <ErrorCode/ErrorCode.h>  
  
using RetType1 = ErrorCode::CustomError<int>; // boost::leaf::result<int>  
using RetType2 = ErrorCode::CustomError<float>; // boost::leaf::result<float>  
  
RetType1 test1(int a) {  
    if (a == 1) {  
        return ErrorCode::newError(ErrorCode::EC::E_GENERIC_UNEXPECTED); // boost::leaf::new_error(ErrorCode::EC::E_GENERIC_UNEXPECTED)  
    }  
    return 32;  
}  
  
RetType2 test2(int a) {  
    return boost::leaf::try_handle_some(  
            [a]() -> RetType2 {  
                BOOST_LEAF_AUTO(val, test1(a));  
                static_cast<void>(val);  
                return 4.5;  
            },  
            [](boost::leaf::match<ErrorCode::EC, ErrorCode::EC::E_GENERIC_UNEXPECTED>) -> RetType2 {  
                std::cerr << "catch err 1\n";  
                return ErrorCode::newError(ErrorCode::EC::E_GENERIC_PARSE); // boost::leaf::new_error(ErrorCode::EC::E_GENERIC_PARSE)  
            }  
    );  
}  
  
void test3(int a) {  
    return boost::leaf::try_handle_all(  
            [a]() -> ErrorCode::Status {  
                BOOST_LEAF_AUTO(val, test2(a));  
                static_cast<void>(val);  
                return {};  
            },  
            [](boost::leaf::match<ErrorCode::EC, ErrorCode::EC::E_GENERIC_PARSE>) {  
                std::cerr << "yeah, correct!\n";  
            },  
            [](ErrorCode::EC e) {  
                std::cerr << "error: " << int(e) << '\n';  
            },  
            []() {  
                std::cerr << "unknown error\n";  
            }  
    );  
}  
  
int main() {  
    test3(1);  
    return 0;  
}  
```  
  
Console output:  
```  
catch err 1  
unknown error  
```  
  
Am I doing something wrong?

---

## Comment 1

> Username: zajo  
> Created at: 2023-04-05 00:25:00 UTC  
> Url: https://github.com/boostorg/leaf/issues/53#issuecomment-1496763490  

This is a bug, thank you. Will fix.

---

## Comment 2

> Username: zajo  
> Created at: 2023-04-05 04:31:34 UTC  
> Url: https://github.com/boostorg/leaf/issues/53#issuecomment-1496903047  

I pushed a fix to the develop branch.

---

## Comment 3

> Username: zajo  
> Created at: 2023-04-09 19:32:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/53#issuecomment-1501197782  

This made it just in time for the 1.82 release candidate, so it will be included in the upcoming release in a few days.
