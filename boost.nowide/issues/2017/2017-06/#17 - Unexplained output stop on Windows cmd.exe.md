# #17 - Unexplained output stop on Windows cmd.exe [Closed]

> Username: norbertwenzel  
> Created at: 2017-06-20 10:08:36 UTC  
> Updated at: 2017-06-20 14:44:01 UTC  
> Closed at: 2017-06-20 14:44:01 UTC  
> Url: https://github.com/boostorg/nowide/issues/17  

When reviewing Nowide I created the following small program:  
```cpp  
#include <boost/nowide/iostream.hpp>  
  
int main(int, char**)  
{  
    using boost::nowide::cout;  
  
    cout << "Hello nowide World!" << std::endl;  
    //when the next line is enabled no output (from here on) is visible in cmd.exe  
    //cout << "Some local codepage special characters: ÄÖÜ äöüß °" << std::endl;  
    cout << u8"Some UTF-8 special characters: ÄÖÜ äöüß °" << std::endl;  
    cout << "Some more exotic characters: " << u8"☃★☁ and ✈" << std::endl;  
    cout << "goodbye nowide world" << std::endl;  
  
    return 0;  
}  
```  
  
When called from Git Bash with UTF-8 character set everything works fine and all output is written as expected.  
When called from Visual Studio (Ctrl+F5) using `cmd.exe` (or whatever ancient shell is used by VS) the output for the above program is fine, except the missing characters are replaced with empty boxes.  
  
But when I enable the commented out line printing special characters in the local codepage (tested on a German Win7, so I guess CP-1252 is used) only the first line is printed. Only the following text is visible:  
```  
Hello nowide World!  
Drücken Sie eine beliebige Taste . . .  
```  
(Where "Drücken Sie eine beliebige Taste..." is the default "Press any key..." output after the program finished.)  
  
When the same executable is run from Git Bash the output is as expected and the local codepage special characters are printed as garbage (as expected).  
```  
Hello nowide World!  
Some local codepage special characters: ▒▒▒ ▒▒▒▒ ▒  
Some UTF-8 special characters: ÄÖÜ äöüß °  
Some more exotic characters: ☃★☁ and ✈  
goodbye nowide world  
```  
  
I assume this is caused by the narrow strings not being valid UTF-8. Checking `cout.good()` indicates that there are some issues after printing the local codepage characters but only when `cmd.exe` is used. I find it very unfortunate that simple encoding errors inhibit any future writes to the stream. Especially if the stream is something like `cout` I think only very few developers actually check the stream and do not expect it to simply be working.  
Since this issue is dependent on the shell/settings used it might be very hard to find and/or reproduce, which probably would lead to customers complaining there is no output anymore.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2017-06-20 13:50:55 UTC  
> Url: https://github.com/boostorg/nowide/issues/17#issuecomment-309759706  

Make sure you use truetype fonts for the console.  
  
No need to change codepage.

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2017-06-20 13:54:56 UTC  
> Url: https://github.com/boostorg/nowide/issues/17#issuecomment-309760981  

Actually it fails to convert local code page as it is not UTF-8 and makes error.  
  
Also as the result of the review invalid characters will be repalced with U-FFFD - invalid character and you'll see ??? instead of stopping the processing see https://github.com/artyom-beilis/nowide/issues/16

---

## Comment 3

> Username: norbertwenzel  
> Created at: 2017-06-20 14:44:01 UTC  
> Url: https://github.com/boostorg/nowide/issues/17#issuecomment-309780042  

Thanks for your quick reply. The missing characters on cmd.exe were expected. But the output stop was not, but the review has already brought that up. Sorry for the duplication here.
