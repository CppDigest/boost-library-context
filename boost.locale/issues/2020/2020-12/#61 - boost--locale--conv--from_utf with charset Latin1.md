# #61 - boost::locale::conv::from_utf with charset Latin1 [Closed]

> Username: GChivot  
> Created at: 2020-12-24 14:39:12 UTC  
> Updated at: 2022-11-17 16:21:08 UTC  
> Closed at: 2022-11-14 09:40:26 UTC  
> Url: https://github.com/boostorg/locale/issues/61  

I write a little test program on windows :   
  
`#include <boost/locale.hpp>  
#include <iostream>  
#include <ctime>  
int main()  
{  
    using namespace boost::locale;  
    using namespace std;  
  
    std::string my_string = "aiou";  
	try  
	{  
        cout << "initial string " << my_string << endl;  
  
        my_string = conv::from_utf(my_string, "Latin1");  
        cout << "This is conversion from utf8 from latin " << my_string << endl;  
  
		my_string = conv::to_utf<char>(my_string,"Latin1");  
        cout << "This is conversion from to from latin " << my_string << endl;  
	}  
	catch(conv::conversion_error e)  
	{  
		std::cout << "Error !" << std::endl;  
	}  
    catch (conv::invalid_charset_error e)  
    {  
        std::cout << "invalid char set error" << std::endl;  
        std::cout << e.what() << std::endl;  
    }  
      
	cout<<"end!"<<endl;  
}`  
  
I tried to run it with mingw and visual studio 2019 (with cmake). On mingw, I have no problems, it works well. On the other hand, with visual studio 2019, the program throw an exception : `Invalid or unsupported charset:Latin1` (version of boost 1.75). it works if replace Latin1 by ISO-8859-1.  
  
According to the documentation (https://www.boost.org/doc/libs/1_73_0/libs/locale/doc/html/charset_handling.html), it looks like a bug for me ... ?

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-11-17 16:21:08 UTC  
> Url: https://github.com/boostorg/locale/issues/61#issuecomment-1318881697  

FYI: This is now fixed and will be in the upcoming 1.81 release.  
  
As for the reason it didn't work on MSVC: Your MinGW version (of Boost) was built with IConv and/or ICU but your MSVC version was not build with either falling back to a hard-coded list of possible codepages and that didn't include (the rather informal) "Latin1" but only the "official" names like "ISO-8859-1"  
  
I did add a small change to make "Latin1" a synonym for "ISO-8859-1" for that case.
