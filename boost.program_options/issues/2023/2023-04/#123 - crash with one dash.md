# #123 - crash with one dash [Open]

> Username: PaltryProgrammer  
> Created at: 2023-04-10 15:17:39 UTC  
> Updated at: 2023-04-10 20:00:10 UTC  
> Url: https://github.com/boostorg/program_options/issues/123  

please see attached GIF demonstrate crash with one dash i.e. to wit if an option is introduced w/ one dash instead of two exception is thrown . below is code executed .  
```  
#include <boost\program_options.hpp>  
#include <map>  
#include <string>  
using namespace std;  
  
const string option_flag = "O";  
const string option_flag_description = string("an option");  
  
const map<string , string> options__description_map = {  
	{option_flag, option_flag_description},  
};  
  
int main(int argc, const char* argv[], const char* envp[])  
{  
	boost::program_options::variables_map vm;  
	string macros;  
	boost::program_options::options_description options_description("bugyCode options");  
	options_description.add_options()  
		(option_flag.c_str(), boost::program_options::value<string>(&macros), option_flag_description.c_str());  
  
	boost::program_options::store(boost::program_options::parse_command_line(argc, argv, options_description), vm);  
	boost::program_options::notify(vm);  
	return 0;  
}  
```  
![crash with one dash demonstration](https://user-images.githubusercontent.com/48535920/230930473-cfa65ee5-d1f6-4d41-8853-ec1cd6405a7b.gif)

---

## Comment 1

> Username: vprus  
> Created at: 2023-04-10 19:14:28 UTC  
> Url: https://github.com/boostorg/program_options/issues/123#issuecomment-1502201634  

Does this problem happen if you use try/catch (and print the exceptions)?

---

## Comment 2

> Username: PaltryProgrammer  
> Created at: 2023-04-10 19:32:14 UTC  
> Url: https://github.com/boostorg/program_options/issues/123#issuecomment-1502220954  

`std::exception.what()` prints out unrecognised option '-O' which by the way is misspelled . -Best

---

## Comment 3

> Username: vprus  
> Created at: 2023-04-10 20:00:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/123#issuecomment-1502246984  

So, it sounds like everything works as designed - the library uses exceptions to report issues, and therefore try/catch block should always be present.
