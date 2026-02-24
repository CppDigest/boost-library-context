# #225 - Gregorian istream exception with empty input [Open]

> Username: lakeweb  
> Created at: 2022-12-10 20:07:52 UTC  
> Updated at: 2022-12-10 20:07:52 UTC  
> Url: https://github.com/boostorg/date_time/issues/225  

I'm using boost::lexical cast to process xml data. The likes of:  
  
```  
template<typename T>  
bool GetAttribute(const pugi::char_t* name, T& attr) const {  
    try {  
        attr = boost::lexical_cast<T>(GetAttribute(name));  
    }  
    catch (boost::bad_lexical_cast& e) {  
        std::cout << "pugi: " << e.what() << std::endl;  
        return false;  
    }  
    return true;  
}  
```  
Which works fine even if there is a bad str like "0". Nice that it leaves whatever default value in attr alone. If this is a new node that does not exist yet, 'GetAttribute(name)' return an empty char* str, "". It is a valid string but instead of throwing it gets down  parse_date and:  
  
format_date_parser.hpp(258)  
`while(std::isspace(*sitr) && sitr != stream_end) { ++sitr; }  
`  
But sitr is not a valid pointer if the input was an empty string. The buffer pointers don't get set up. For now, I'm hacking this code, checking that 'sitr' is not null, and throwing if it is. I'm just thinking that should be part of the Gregorian parser. And I don't know but this may have never really shown because linux std_libs::istream hand you a valid pointer in this circumstance.  
  
Best, Dan.
