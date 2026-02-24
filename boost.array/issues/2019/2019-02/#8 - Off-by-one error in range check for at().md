# #8 - Off-by-one error in range check for at() [Closed]

> Username: DHilbrich  
> Created at: 2019-02-07 15:32:35 UTC  
> Updated at: 2019-02-07 15:53:15 UTC  
> Closed at: 2019-02-07 15:53:15 UTC  
> Url: https://github.com/boostorg/array/issues/8  

Hello, there is an off-by-one error in the range check for at(). The function at() doesn't throw an exception if the index is the size of the array.  
The following program doesn't throw the expected exception.  
  
    #include <boost/array.hpp>  
    #include <iostream>  
      
    int main()  
    {  
           boost::array<int,1> a;  
           try {  
                    a.at(1) = 12;  
            } catch (const std::out_of_range& e) {  
                    std::cerr << e.what() << std::endl;  
            }  
            return 0;  
    }  
  
The cause of this error is a wrong comparison in rangecheck(), which can be fixed with following patch:  
  
    diff --git a/include/boost/array.hpp b/include/boost/array.hpp  
    index 210c072..99dc2c6 100644  
    --- a/include/boost/array.hpp  
    +++ b/include/boost/array.hpp  
    @@ -183,7 +183,7 @@ namespace boost {  
       
             // check range (may be private because it is static)  
             static BOOST_CONSTEXPR bool rangecheck (size_type i) {  
    -            return i > size() ? boost::throw_exception(std::out_of_range ("array<>: index out of range")), true : true;  
    +            return i >= size() ? boost::throw_exception(std::out_of_range ("array<>: index out of range")), true : true;  
             }  
       
         };

---

## Comment 1

> Username: mclow  
> Created at: 2019-02-07 15:53:15 UTC  
> Url: https://github.com/boostorg/array/issues/8#issuecomment-461481586  

Thanks. Fixed in b279a90
