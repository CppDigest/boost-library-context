# #86 - Parsing invalid JSON results in segmentation fault. [Closed]

> Username: nilsegger  
> Created at: 2020-05-02 13:05:23 UTC  
> Updated at: 2020-05-02 14:36:54 UTC  
> Closed at: 2020-05-02 14:36:54 UTC  
> Url: https://github.com/boostorg/json/issues/86  

I have been trying to parse user generated json, which obviously could be faulty. In this case I tried parsing a json string, which is missing a separating comma between two values.  
  
Example:   
```c++  
  
#include <string>  
#include <boost/json.hpp>  
  
int main(int argc, char *argv[]) {  
  
    std::string test = "{"  
                       "\"username\": \"user\"" // <- Missing separating ,  
                       "\"password\": \"password\""  
                       "}";  
    boost::json::error_code ec;  
    boost::json::parser parser;  
    parser.start();  
    parser.write(test.c_str(), test.size(), ec);  
    parser.finish();  
  
    if (ec) {  
        std::cout << ec.message() << std::endl;  
        return EXIT_FAILURE;  
    }  
  
    auto const jv = parser.release();  
  
    if (ec) {  
        std::cout << ec.message() << std::endl;  
        return EXIT_FAILURE;  
    } else {  
        std::cout << jv << std::endl;  
        return EXIT_SUCCESS;  
    }  
}  
```  
  I tried both parse and parser, both resulting in segmentation fault. I checked the docs about how I should validate the json, but all I could find was either using error_codes or exceptions.  
  
Am I missing a function to check untrusted json for errors or is there a bug in the library?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-02 13:32:48 UTC  
> Url: https://github.com/boostorg/json/issues/86#issuecomment-622954418  

Impressive! This sounds like a bug in the library.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-05-02 13:42:29 UTC  
> Url: https://github.com/boostorg/json/issues/86#issuecomment-622955725  

Upon further investigation, the problem is with your code. You are calling `finish()` without checking the error returned from `write`. This will fix your code:  
```  
    parser.start();  
    parser.write(test.c_str(), test.size(), ec);  
    if( ! ec )  
        parser.finish( ec );  
```  
  
Notice that I pass `ec` to `finish` above. In your code, you were calling the version of `finish` which throws an exception on error. I doubt this was your intent.

---

## Comment 3

> Username: nilsegger  
> Created at: 2020-05-02 14:18:26 UTC  
> Url: https://github.com/boostorg/json/issues/86#issuecomment-622960594  

Yes seems to fix it on my side aswell, but what about the other parse function.  
  
```c++  
std::string test = "{"  
                       "\"username\": \"user\"" // <- Missing separating ,  
                       "\"password\": \"password\""  
                       "}";  
    boost::json::error_code ec;  
    const auto jv = boost::json::parse(test, ec);  
  
    if (ec) {  
        std::cout << ec.message() << std::endl;  
        return EXIT_FAILURE;  
    } else {  
        std::cout << jv << std::endl;  
        return EXIT_SUCCESS;  
    }  
```  
This ends up in a segmentation fault aswell.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-05-02 14:21:16 UTC  
> Url: https://github.com/boostorg/json/issues/86#issuecomment-622960983  

`json::parse` works fine for me. Are you using the latest version from the repository?

---

## Comment 5

> Username: nilsegger  
> Created at: 2020-05-02 14:36:54 UTC  
> Url: https://github.com/boostorg/json/issues/86#issuecomment-622963069  

Hmm yes I did download it extra before submitting this issue. I will do some further inspection on my side. Thanks for your help.
