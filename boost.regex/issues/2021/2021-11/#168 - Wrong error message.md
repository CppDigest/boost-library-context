# #168 - Wrong error message [Open]

> Username: olivier-75  
> Created at: 2021-11-12 08:04:38 UTC  
> Updated at: 2021-11-12 08:04:38 UTC  
> Url: https://github.com/boostorg/regex/issues/168  

Windows Platform, using gcc 11.2 and boost 1.77  
  
With regex  [a-e the error message is wrong, referencing unbalanced parenthesis, instead of unmatched [  
Error message : Found a closing ) with no corresponding opening parenthesis.  The error occurred while parsing the regular expression: '[a-e>>>HERE>>>'. at 4  
  
`#include <boost/regex.hpp>  
#include <string>  
#include <iostream>  
  
int main()  
{  
	std::string s = "Boost Libraries Test";  
	try  
	{  
		// regex contains error : missing ]  
		boost::regex expr{"[a-e"};  
		boost::smatch what;  
		if (boost::regex_search(s, what, expr))  
			std::cout << "Found !" << '\n';  
		else  
			std::cout << "Nothing !" << '\n';  
	}  
	catch(boost::regex_error& ex)  
	{  
		std::cout << ex.what() << " at " << ex.position() << "\n";  
	}  
}`
