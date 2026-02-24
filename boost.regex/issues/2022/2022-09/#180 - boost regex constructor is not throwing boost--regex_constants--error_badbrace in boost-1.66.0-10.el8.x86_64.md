# #180 - boost regex constructor is not throwing boost::regex_constants::error_badbrace in boost-1.66.0-10.el8.x86_64 [Closed]

> Username: lohitendu2000  
> Created at: 2022-09-29 04:43:11 UTC  
> Updated at: 2022-10-10 04:03:24 UTC  
> Closed at: 2022-10-10 04:03:12 UTC  
> Url: https://github.com/boostorg/regex/issues/180  

This is in reference with issue 179

---

## Comment 1

> Username: lohitendu2000  
> Created at: 2022-09-29 04:45:25 UTC  
> Url: https://github.com/boostorg/regex/issues/180#issuecomment-1261747495  

This following program I have compiled and run in two versions of boost, which is boost-1.53.0-27.el7.x86_64 and boost-1.66.0-10.el8.x86_64  
  
#include <boost/regex.hpp>  
#include  
#include  
  
int main()  
{  
try {  
std::string value = "^[a-z0-9]{a,z}$";  
boost::regex pattern(value);  
}  
catch (boost::regex_error &e)  
{  
std::cout<<"the error caught is "<<e.what()<<std::endl;  
return 0;  
}  
std::cout<<"no error caught"<<std::endl;  
return 0;  
}  
  
Output when boost is in version 1.53  
the error caught is Invalid content of repeat range.  The error occurred while parsing the regular expression: '^[a-z0-9]{>>>HERE>>>a,z}$'.  
  
Output when boost is in version 1.66  
no error caught

---

## Comment 2

> Username: lohitendu2000  
> Created at: 2022-10-10 04:03:24 UTC  
> Url: https://github.com/boostorg/regex/issues/180#issuecomment-1272757363  

same as 179
