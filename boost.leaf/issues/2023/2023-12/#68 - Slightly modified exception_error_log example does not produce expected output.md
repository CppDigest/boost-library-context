# #68 - Slightly modified exception_error_log example does not produce expected output [Closed]

> Username: arcrc  
> Created at: 2023-12-01 12:50:20 UTC  
> Updated at: 2023-12-10 16:45:58 UTC  
> Closed at: 2023-12-10 16:45:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/68  

Hi,   
  
I have slightly modified the following example https://github.com/boostorg/leaf/blob/master/example/error_log.cpp?ts=4 -> https://godbolt.org/z/48q9dnfTc  
(f5() now calls f4() but discards its result and returns {})  
  
However, even though the error should not reach the error logging handler, the error messages are printed. This is an example output:  
  
**Program stdout**  
Run # 0: Success!  
Run # 1: Success!  
Run # 2: Success!  
Run # 3: Success!  
Run # 4: Success!  
Run # 5: Success!  
Run # 6: Success!  
Run # 7: Success!  
Run # 8: Success!  
Run # 9: Success!  
  
**Program stderr**  
Error! Log:  
/app/example.cpp(64)  
/app/example.cpp(73)  
/app/example.cpp(82)  
/app/example.cpp(91)  
/app/example.cpp(100)  
Error! Log:  
/app/example.cpp(82)  
/app/example.cpp(91)  
/app/example.cpp(100)  
Error! Log:  
/app/example.cpp(73)  
/app/example.cpp(82)  
/app/example.cpp(91)  
/app/example.cpp(100)  
Error! Log:  
/app/example.cpp(73)  
/app/example.cpp(82)  
/app/example.cpp(91)  
/app/example.cpp(100)  
Error! Log:  
/app/example.cpp(64)  
/app/example.cpp(73)  
/app/example.cpp(82)  
/app/example.cpp(91)  
/app/example.cpp(100)  
Error! Log:  
/app/example.cpp(91)  
/app/example.cpp(100)  
  
**Expected output**: there should be no entry in stderr  
  
  
Code in case godbolt not accessible  
```  
// Copyright 2018-2023 Emil Dotchevski and Reverge Studios, Inc.  
  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
// This program demonstrates the use of leaf::on_error to print the path an  
// error takes as it bubbles up the call stack. The printing code only runs if:  
// - An error occurs, and  
// - A handler that takes e_error_log argument is present. Otherwise none of the  
//   error log machinery will be invoked by LEAF.  
  
// This example is similar to error_trace, except the path the error takes is  
// not captured, only printed.  
  
#include <boost/leaf.hpp>  
#include <iostream>  
#include <cstdlib>  
  
#define ENABLE_ERROR_LOG 1  
  
namespace leaf = boost::leaf;  
  
// The error log is activated only if an error handling scope provides a handler  
// for e_error_log.  
struct e_error_log  
{  
    struct rec  
    {  
        char const * file;  
        int line;  
        friend std::ostream & operator<<( std::ostream & os, rec const & x )  
        {  
            return os << x.file << '(' << x.line << ')';  
        }  
    };  
  
    e_error_log()  
    {  
        std::cerr << "Error! Log:" << std::endl;  
    }  
  
    // Our e_error_log instance is stateless, used only as a target to  
    // operator<<.  
    template <class T>  
    friend std::ostream & operator<<( e_error_log const &, T const & x )  
    {  
        return std::cerr << x << std::endl;  
    }  
};  
  
// The ERROR_LOG macro is designed for use in functions that detect or forward  
// errors up the call stack. If an error occurs, and if an error handling scope  
// provides a handler for e_error_log, the supplied lambda is executed as the  
// error bubbles up.  
#define ERROR_LOG auto _log = leaf::on_error( []( e_error_log & log ) { log << e_error_log::rec{__FILE__, __LINE__}; } )  
  
// Each function in the sequence below calls the previous function, and each  
// function has failure_percent chance of failing. If a failure occurs, the  
// ERROR_LOG macro will cause the path the error takes to be printed.  
int const failure_percent = 25;  
  
leaf::result<void> f1()  
{  
    ERROR_LOG;  
    if( (std::rand()%100) > failure_percent )  
        return { };  
    else  
        return leaf::new_error();  
}  
  
leaf::result<void> f2()  
{  
    ERROR_LOG;  
    if( (std::rand()%100) > failure_percent )  
        return f1();  
    else  
        return leaf::new_error();  
}  
  
leaf::result<void> f3()  
{  
    ERROR_LOG;  
    if( (std::rand()%100) > failure_percent )  
        return f2();  
    else  
        return leaf::new_error();  
}  
  
leaf::result<void> f4()  
{  
    ERROR_LOG;  
    if( (std::rand()%100) > failure_percent )  
        return f3();  
    else  
        return leaf::new_error();  
}  
  
leaf::result<void> f5()  
{  
    ERROR_LOG;  
    f4();  
    return {};  
}  
  
int main()  
{  
    for( int i=0; i!=10; ++i )  
        leaf::try_handle_all(  
            [&]() -> leaf::result<void>  
            {  
                std::cout << "Run # " << i << ": ";  
                BOOST_LEAF_CHECK(f5());  
                std::cout << "Success!" << std::endl;  
                return { };  
            },  
#if ENABLE_ERROR_LOG // This single #if enables or disables the printing of the error log.  
            []( e_error_log const & )  
            {  
            },  
#endif  
            []  
            {  
                std::cerr << "Error!" << std::endl;  
            } );  
    return 0;  
}  
  
////////////////////////////////////////  
  
#ifdef BOOST_LEAF_NO_EXCEPTIONS  
  
namespace boost  
{  
    [[noreturn]] void throw_exception( std::exception const & e )  
    {  
        std::cerr << "Terminating due to a C++ exception under BOOST_LEAF_NO_EXCEPTIONS: " << e.what();  
        std::terminate();  
    }  
  
    struct source_location;  
    [[noreturn]] void throw_exception( std::exception const & e, boost::source_location const & )  
    {  
        throw_exception(e);  
    }  
}  
  
#endif  
```

---

## Comment 1

> Username: zajo  
> Created at: 2023-12-01 20:56:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/68#issuecomment-1836764937  

The point of this example is to demonstrate that the logging is enabled by the presence of the e_error_log handler. Presumably such a handler would only be present if the feature is enabled, thus providing a single point in the code that controls the logging. However, logging occurs even if the error is ignored before it reaches the handler.  
  
If you want the logging to only occur if the error reaches the handler, the solution is to accumulate the log and print it in the error handler, like this: https://godbolt.org/z/M7vqfeYGv.

---

## Comment 2

> Username: arcrc  
> Created at: 2023-12-02 14:14:50 UTC  
> Updated at: 2023-12-03 17:14:24 UTC  
> Url: https://github.com/boostorg/leaf/issues/68#issuecomment-1837158505  

I am a little puzzled. I thought that error handlers become active only if a matching error is returned from `TryBlock`. Now it seems that lambdas passed to `leaf::on_error(...)` are executed before considering  `TryBlock`, which means that errors are already loaded in the context.   
On the other hand, handler body is executed after evaluating `TryBlock` and that's why the solution in https://godbolt.org/z/M7vqfeYGv yields in expected output.    
However, this also means that in certain scenarios (if no errors are detected in `TryBlock`) unnecessary work is performed, as `append` is executed. Can this be avoided?  
  
Edit:  
I analyzed the `on_error` functionality using debugger and I finally got the idea. Storage for the error is created by supplying a trivial handler (`[]( e_error_log const & log ) {}`) to `try_handle...(...)` which internally creates ctx. Then, in low-level function, once an object associated with  `on_error` is about to be destructed it is checked if the error occurred. If so it is further checked what has been supplied as an argument to `on_error`. If it is a lambda taking mutable reference to the error object and its storage is not initialized, a default constructor creates the object and the lambda's body is executed. This fully justifies why the work specified in `on_error` is executed even though the result of `TryBlock` is error-free.  
  
Regarding my previous question, `append` is only collecting input for the potential further processing inside the corresponding error handler, which means there should not be a big overhead, even if it is in the end discarded. This probably cannot be avoided as low-level function has no context to decide if this data is to be used and the object created via `on_error` cannot magically appear back in the handler's body.  
  
Thank you for the solution and in case there is no alternative approach feel free to close the issue.

---

## Comment 3

> Username: zajo  
> Created at: 2023-12-07 07:13:29 UTC  
> Url: https://github.com/boostorg/leaf/issues/68#issuecomment-1844794593  

The reason this work is performed at the time the lambda is executed is that the error object may have to be created in a context that is not the first one to handle errors. Consider:  
  
```  
struct e_foo { };  
struct e_bar { };  
  
try_catch(  
    []  
    {  
        try_catch(  
        {  
            auto load on_error([]( e_foo & ) { .... });  
            leaf::throw_exception(e_bar{});  
        },  
        []( e_bar & ) // 1  
        {  
            throw;  
        } );  
    },  
    []( e_foo &, e_bar & ) // 2  
    {  
    } );  
    
```  
  
Above, the `throw_exception` call will move `e_bar` first in the inner context so it can be passed to the error handler marked as `// 1`, which rethrows the exception (the error is not handled), so `e_bar` is moved from the inner context to the outer context. But `e_foo` doesn't get created in the inner context, there's no storage for it there! So it is created directly in the outer context, waiting for `e_bar` to arrive so both can be passed to the error handler marked as `// 2`.  
  
If now you remove the `throw;` statement, the error is handled - and the outer `try_catch` won't be handling any errors, but by now `e_foo` is already there (and will be destroyed when the outer `try_catch` returns).
