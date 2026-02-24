# #493 - boost 1.70 multiprecision  does not work with 64 bit compiler under C++ builder 10.4 [Closed]

> Username: davidlewistoronto  
> Created at: 2022-09-12 16:09:28 UTC  
> Updated at: 2022-10-11 08:07:05 UTC  
> Closed at: 2022-09-13 15:49:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493  

C++ builder community edition is 10.4 and can include boost 1.70. 32b compiles of multiprecision work but 64b do not. Example with some usual #includes not included. Perhaps 1.70 is obsolete but this is the only version that is available to C++ builder community edition. If any fix is known, such as compiling boost for C++ builder it would be appreciated. Link errors are:  
  
  
[ilink64 Error] Error: Unresolved external '__udivti3' referenced from C:\USERS\DAVID\LOCAL_DEVEL\BOOST_TEST\WIN64\DEBUG\FILE1.O  
[ilink64 Error] Error: Unresolved external '__umodti3' referenced from C:\USERS\DAVID\LOCAL_DEVEL\BOOST_TEST\WIN64\DEBUG\FILE1.O  
  
      //---------------------------------------------------------------------------  
      #include <boost/multiprecision/cpp_int.hpp>  
      #include <boost/multiprecision/cpp_bin_float.hpp>  
      #include <boost/rational.hpp>  
        
      using namespace std;  
      using namespace boost::multiprecision;  
      using boost::rational;  
        
        
        
      typedef boost::rational<cpp_int> t_prob;  
      int main (int argc, char *argv [])  
      {   t_prob a,b,c;  
        
              a = 4;  
      //      a /= 6;  
              b = 13;  
      //      b /= 7;  
              c = a * b;  
          std::cout << c << "\n";  
        
      }

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-09-12 17:51:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1244094307  

I tried this with C++ Builder 10.4 update 2, and the latest Boost-1.80, and other than a warning about the compiler version being unknown, everything worked just fine in both 32 and 64 bit builds.  
  
To test, I created a new GUI project using the default template, and just added your code to the form constructor, so the unit looks like:  
  
```  
#include <vcl.h>  
#pragma hdrstop  
#include "Unit1.h"  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/rational.hpp>  
  
using namespace std;  
using namespace boost::multiprecision;  
using boost::rational;  
  
  //---------------------------------------------------------------------------  
#pragma package(smart_init)  
#pragma resource "*.dfm"  
TForm1 *Form1;  
//---------------------------------------------------------------------------  
__fastcall TForm1::TForm1(TComponent* Owner)  
	: TForm(Owner)  
{  
  typedef boost::rational<cpp_int> t_prob;  
  t_prob a,b,c;  
  
		  a = 4;  
		  a /= 6;  
		  b = 13;  
		  b /= 7;  
		  c = a * b;  
}  
```  
  
What were the errors that you saw?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-09-12 17:53:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1244095854  

Forgot to say, in the latest Boost code as a general point, cpp_rational is likely to be slightly more efficient than a boost::rational of cpp_int's.

---

## Comment 3

> Username: davidlewistoronto  
> Created at: 2022-09-12 18:29:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1244134511  

Thank you so much for replying. The errors are two link errors  
  
[ilink64 Error] Error: Unresolved external '__udivti3' referenced from C:\USERS\DAVID\LOCAL_DEVEL\BOOST_TEST\WIN64\DEBUG\FILE1.O  
[ilink64 Error] Error: Unresolved external '__umodti3' referenced from C:\USERS\DAVID\LOCAL_DEVEL\BOOST_TEST\WIN64\DEBUG\FILE1.O  
  
   
  
I am forced to use 1.70 as that is what the GetIt manager provides. Can you please explain how to get 1.80? I’m using c++ builder community so they  
  
may not make it easy to get the latest Boost.  
  
   
  
Also thanks for the info about cpp_rational. I’ll have to try that.  
  
   
  
Dave  
  
   
  
   
  
From: jzmaddock ***@***.***>   
Sent: September 12, 2022 1:54 PM  
To: boostorg/multiprecision ***@***.***>  
Cc: davidlewistoronto ***@***.***>; Author ***@***.***>  
Subject: Re: [boostorg/multiprecision] boost 1.70 multiprecision does not work with 64 bit compiler under C++ builder 10.4 (Issue #493)  
  
   
  
Forgot to say, in the latest Boost code as a general point, cpp_rational is likely to be slightly more efficient than a boost::rational of cpp_int's.  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/multiprecision/issues/493#issuecomment-1244095854> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/APBBKXKZMVUFNIF7D3B4WBDV55UZ5ANCNFSM6AAAAAAQKT3KRA> .  
You are receiving this because you authored the thread.  <https://github.com/notifications/beacon/APBBKXMQR63RZFNCJGJWRYLV55UZ5A5CNFSM6AAAAAAQKT3KRCWGG33NNVSW45C7OR4XAZNMJFZXG5LFINXW23LFNZ2KUY3PNVWWK3TUL5UWJTSKE5SW4.gif> Message ID: ***@***.*** ***@***.***> >

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-09-13 08:09:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1245052478  

You can download the latest Boost release from www.boost.org, then just add the directory you extract that to to your #include path in the project options.  
  
BTW I just tried some earlier versions of Boost and hit no end of errors, so I think upgrading is the way to go.

---

## Comment 5

> Username: davidlewistoronto  
> Created at: 2022-09-13 11:48:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1245298873  

Thanks for the help. I did consider that, but was afraid the binaries might not be compatible, and it might overwrite Embarcadaro’s.  
  
If you have been successful, that sounds like the way to go.  
  
   
  
Dave  
  
   
  
   
  
From: jzmaddock ***@***.***>   
Sent: September 13, 2022 4:10 AM  
To: boostorg/multiprecision ***@***.***>  
Cc: davidlewistoronto ***@***.***>; Author ***@***.***>  
Subject: Re: [boostorg/multiprecision] boost 1.70 multiprecision does not work with 64 bit compiler under C++ builder 10.4 (Issue #493)  
  
   
  
You can download the latest Boost release from www.boost.org <http://www.boost.org> , then just add the directory you extract that to to your #include path in the project options.  
  
BTW I just tried some earlier versions of Boost and hit no end of errors, so I think upgrading is the way to go.  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/multiprecision/issues/493#issuecomment-1245052478> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/APBBKXIY5RPGFICFXKQRMXLV6AZFJANCNFSM6AAAAAAQKT3KRA> .  
You are receiving this because you authored the thread.  <https://github.com/notifications/beacon/APBBKXNA4E44J34RDVO3X2LV6AZFJA5CNFSM6AAAAAAQKT3KRCWGG33NNVSW45C7OR4XAZNMJFZXG5LFINXW23LFNZ2KUY3PNVWWK3TUL5UWJTSKGX7D4.gif> Message ID: ***@***.*** ***@***.***> >

---

## Comment 6

> Username: jzmaddock  
> Created at: 2022-09-13 12:12:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1245325331  

There are no binaries in the zip from the website, and in any case, Multiprecision is header only, no binaries required.    
  
If you need binaries for other libraries, then see https://www.boost.org/doc/libs/1_80_0/more/getting_started/windows.html but also in most cases, the libraries can be treated as "just a bunch of source files" and built as libraries from within your favorite IDE, or simply added direct to your application.  
  
BTW we don't actually use or call the functions such as `__udivti3` that created your original linker errors, those function calls are generated by the compiler itself.

---

## Comment 7

> Username: davidlewistoronto  
> Created at: 2022-09-13 12:18:43 UTC  
> Updated at: 2022-09-13 12:20:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1245331973  

Thanks again, I am just looking through the docs now.  
  
I did find some .dll's buried in the C++ builder files which gave me the concern.  
  
   
  
   
  
From: jzmaddock ***@***.***>   
Sent: September 13, 2022 8:13 AM  
To: boostorg/multiprecision ***@***.***>  
Cc: davidlewistoronto ***@***.***>; Author ***@***.***>  
Subject: Re: [boostorg/multiprecision] boost 1.70 multiprecision does not work with 64 bit compiler under C++ builder 10.4 (Issue #493)  
  
   
  
There are no binaries in the zip from the website, and in any case, Multiprecision is header only, no binaries required.  
  
If you need binaries for other libraries, then see https://www.boost.org/doc/libs/1_80_0/more/getting_started/windows.html but also in most cases, the libraries can be treated as "just a bunch of source files" and built as libraries from within your favorite IDE, or simply added direct to your application.  
  
BTW we don't actually use or call the functions such as __udivti3 that created your original linker errors, those function calls are generated by the compiler itself.  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/multiprecision/issues/493#issuecomment-1245325331> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/APBBKXKKVEPEOYCCIFJ2FT3V6BVT5ANCNFSM6AAAAAAQKT3KRA> .  
You are receiving this because you authored the thread.  <https://github.com/notifications/beacon/APBBKXL7KYFU4M6NI74GQOTV6BVT5A5CNFSM6AAAAAAQKT3KRCWGG33NNVSW45C7OR4XAZNMJFZXG5LFINXW23LFNZ2KUY3PNVWWK3TUL5UWJTSKHIUBG.gif> Message ID: ***@***.*** ***@***.***> >

---

## Comment 8

> Username: davidlewistoronto  
> Created at: 2022-09-13 12:58:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1245378373  

> There are no binaries in the zip from the website, and in any case, Multiprecision is header only, no binaries required.  
>   
> If you need binaries for other libraries, then see https://www.boost.org/doc/libs/1_80_0/more/getting_started/windows.html but also in most cases, the libraries can be treated as "just a bunch of source files" and built as libraries from within your favorite IDE, or simply added direct to your application.  
>   
> BTW we don't actually use or call the functions such as `__udivti3` that created your original linker errors, those function calls are generated by the compiler itself.  
  
It takes nearly forever to unzip, but it works! Other than  
  
[bcc64 Warning] codegear.hpp(19): boost: Unknown compiler version - please run the configure tests and report the results  
  
which I can ignore or solve.  
  
Thanks again.

---

## Comment 9

> Username: davidlewistoronto  
> Created at: 2022-10-11 08:07:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/493#issuecomment-1274269142  

It works, after taking more than 30 minutes to unzip! Other than  
  
   
  
[bcc64 Warning] codegear.hpp(19): boost: Unknown compiler version - please run the configure tests and report the results  
  
   
  
which is unimportant and probably solveable.  
  
   
  
Thanks again.  
  
   
  
   
  
From: jzmaddock ***@***.***>   
Sent: September 13, 2022 8:13 AM  
To: boostorg/multiprecision ***@***.***>  
Cc: davidlewistoronto ***@***.***>; Author ***@***.***>  
Subject: Re: [boostorg/multiprecision] boost 1.70 multiprecision does not work with 64 bit compiler under C++ builder 10.4 (Issue #493)  
  
   
  
There are no binaries in the zip from the website, and in any case, Multiprecision is header only, no binaries required.  
  
If you need binaries for other libraries, then see https://www.boost.org/doc/libs/1_80_0/more/getting_started/windows.html but also in most cases, the libraries can be treated as "just a bunch of source files" and built as libraries from within your favorite IDE, or simply added direct to your application.  
  
BTW we don't actually use or call the functions such as __udivti3 that created your original linker errors, those function calls are generated by the compiler itself.  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/multiprecision/issues/493#issuecomment-1245325331> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/APBBKXKKVEPEOYCCIFJ2FT3V6BVT5ANCNFSM6AAAAAAQKT3KRA> .  
You are receiving this because you authored the thread.  <https://github.com/notifications/beacon/APBBKXL7KYFU4M6NI74GQOTV6BVT5A5CNFSM6AAAAAAQKT3KRCWGG33NNVSW45C7OR4XAZNMJFZXG5LFINXW23LFNZ2KUY3PNVWWK3TUL5UWJTSKHIUBG.gif> Message ID: ***@***.*** ***@***.***> >
