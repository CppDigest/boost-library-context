# #282 - Bug from ML [Closed]

> Username: mborland  
> Created at: 2025-10-10 15:36:03 UTC  
> Updated at: 2025-10-23 12:15:41 UTC  
> Closed at: 2025-10-23 12:15:41 UTC  
> Url: https://github.com/boostorg/charconv/issues/282  

Hi,  
  
the following program prints "1e-01". Furthermore, it prints "Error:  
132" if I change `size` to 20. It prints the expected "0.1" if I change  
the two using declarations to use the standard facilities. Tested  
against the master branch, with MSVC 2022.  
  
Is this a bug in CharConv?  
  
   #include "boost/charconv.hpp"  
   #include <charconv>  
   #include <iostream>  
  
   int  
   main()  
   {  
       // using std::to_chars;  
       // using std::chars_format::general;  
       using boost::charconv::to_chars;  
       using boost::charconv::chars_format::general;  
  
       int const size = 100;  
       char buffer[ size ];  
       auto const r = to_chars( buffer, buffer + size, 0.1, general );  
       if ( [r.ec](http://r.ec/) != std::errc() )  
       {  
           std::cerr << "Error: " << static_cast< int >( [r.ec](http://r.ec/) )  
             << std::endl;  
           return 1;  
       }  
       *r.ptr = '\0';  
       std::cout << buffer << std::endl;  
   }
