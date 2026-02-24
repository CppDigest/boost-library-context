# #24 - Problems with displaying long option description with cyrillic characters [Open]

> Username: artempervin  
> Created at: 2016-12-01 14:09:19 UTC  
> Updated at: 2017-10-28 17:37:51 UTC  
> Url: https://github.com/boostorg/program_options/issues/24  

I'm having some weird issues with displaying long option description that contains cyrillic characters:  
  
```  
[program_options]$ ./example --help  
Allowed options:  
  -h [ --help ]                      print usage message  
  -f [ --enforcewrite ] enforcewrite Всегда перезаписывать  
                                     файлы в директории с  
                                     преобразованной  
                                     политикой безопасност▒  
                                     ▒  
```  
Note that weird symbols at the description end.  
Here's the code to reproduce it:   
  
```  
#include <boost/program_options.hpp>  
using namespace boost::program_options;  
  
#include <iostream>  
using namespace std;  
  
int main(int argc, char* argv[])  
{  
    string s;  
  
    options_description desc("Allowed options");  
    desc.add_options()  
        ("help,h", "print usage message")  
        ("enforcewrite,f", value(&s)->value_name("enforcewrite"),  
        "Всегда перезаписывать файлы в директории с преобразованной политикой безопасности");  
  
    variables_map vm;  
    store(parse_command_line(argc, argv, desc), vm);  
  
    if (vm.count("help")) {  
        cout << desc << "\n";  
        return 0;  
    }  
}  
```  
  
Appearance of such symbols seems to depend somehow on the length of the text string associated with the option (including option name, default value and it's description). In my case I worked around the problem by increasing the column width.  
  
program option version is: 1.62.0  
OS: CentOS 7.1

---

## Comment 1

> Username: vprus  
> Created at: 2017-07-25 15:28:35 UTC  
> Url: https://github.com/boostorg/program_options/issues/24#issuecomment-317775590  

Can you tell what encoding is used for your source file, and what is your terminal encoding?

---

## Comment 2

> Username: jagerman  
> Created at: 2017-10-28 17:37:51 UTC  
> Url: https://github.com/boostorg/program_options/issues/24#issuecomment-340207573  

As far as I can tell, there is simply no support whatsoever for non-ascii option descriptions.  In this case, the text is being wrapped in the middle of a utf8 multibyte sequence, resulting in two invalid utf8 sequences and thus the "weird symbols" (which is probably your terminal trying to recover from a bad utf8 encoding).  
  
Fundamentally this happens because `option_description` simply has no unicode support at all: it only accepts `char *` arguments for name and description, which it assumes are ascii when wrapping them.  
  
Please consider this a request for properly supporting unicode for all the bits presented to the user (rather than just the bits input from users); this includes, at least, option descriptions and things like `typed_value<T>::value_name(...)`.  
  
(I realize all this is pretty hard: for example, even if `option_description` gained `wchar *` constructors, there is still the issue that the number of codepoints is not necessarily equal to the number of output characters: for example a decomposed `é` consists of the two codepoints U+0065 U+0301, while the composed `é` contains just one codepoint U+00E9; and `ｅ` is one codepoint (U+FF45) that takes up two character spaces in a terminal.  So yeah, there's all these weird bits, but it would be nice to have at least somewhat better wrapping, with some caveats).
