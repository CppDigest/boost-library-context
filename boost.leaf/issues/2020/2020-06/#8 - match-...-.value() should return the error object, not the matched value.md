# #8 - match<...>.value() should return the error object, not the matched value [Closed]

> Username: steveire  
> Created at: 2020-06-22 23:18:40 UTC  
> Updated at: 2020-06-30 06:34:30 UTC  
> Closed at: 2020-06-30 06:34:30 UTC  
> Url: https://github.com/boostorg/leaf/issues/8  

`match<E, V...>::value()` currently returns the matches value, which I wrote in my lambda, but it should return the error object which matched.  
  
Consider https://godbolt.org/z/it5qjZ  
  
We have   
  
```  
enum ErrorKind  
{  
    NoError,  
    SomeError,  
    OtherError  
};  
  
struct MyErrorType  
{  
    ErrorKind value;  
    std::string Payload;  
};  
```  
and   
```  
        ...  
        [] (leaf::match<MyErrorType, ErrorKind::OtherError> Err) {  
            std::cout << "Matched: " << Err.value() << std::endl;  
            return -1;  
        },  
        ...  
```  
  
Currently `Err.value()` returns exactly what I wrote on the line above: `ErrorKind::SomeError`.   
  
In the other case with `leaf::match<MyErrorType, ErrorKind::SomeError, ErrorKind::MoreError> Err)`, `Err.value()` returns whichever of `ErrorKind::SomeError` or `ErrorKind::MoreError` actually matched.  
  
In all cases, the actual error object is not accessible. As I am matching on the type of the error object, I am aware of the MyErrorType itself and it is probably public API and I can know the members of it. That means I can use `Err.value().value` to access what is currently available as `Err.value` but I can also `Err.value().Payload` to access the payload in the above case.  
  
There is no loss of functionality by making the error instance available in error handlers. There is a gain in functionality.  
  
This is true for any error type. I encountered it with `std::error_code` because I needed to access the `message` of the `error_code`. If `match<E, V...>::value()` returned the `std::error_code` itself, I could write:  
  
```  
        [] (boost::leaf::match<std::error_code, FileResult::FileNotFound> err)  
        {  
            std::error_code errc = err.value();  
            std::cout << "File error: " << errc.message() << std::endl;  
            return -1;  
        },  
```  
  
As it is `err.value()` returns me `FileResult::FileNotFound`. It is not useful for the framework to tell me what I just told it.
