# #13 - Running inspect with no arguments causes an exception, should print usage message [Open]

> Username: HDembinski  
> Created at: 2019-08-22 09:14:05 UTC  
> Updated at: 2019-08-22 09:14:45 UTC  
> Url: https://github.com/boostorg/inspect/issues/13  

Running the inspect tool without arguments from the boost root project folder terminates with an exception on my computer.  
  
```  
libc++abi.dylib: terminating with uncaught exception of type std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >  
Abort trap: 6  
```  
  
The expected behavior is to print the usage message.
