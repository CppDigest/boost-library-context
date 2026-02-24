# #206 - Example program parsing_into_a_class.cpp fails to execute correctly under MSVC. [Closed]

> Username: cfmileto  
> Created at: 2024-11-24 18:30:29 UTC  
> Updated at: 2024-11-30 01:32:57 UTC  
> Closed at: 2024-11-25 23:44:16 UTC  
> Url: https://github.com/boostorg/parser/issues/206  

The example program parsing_into_a_class.cpp compiles correctly under MSVC but does not execute correctly.  
  
I was able to get the first parser (string_uint_unit) in the example to execute correctly if I enclosed it inside the string_view directive.  
  
Also, the second parser (uint_string) does not execute correctly.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-11-24 19:37:23 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2496196934  

Thanks for reporting this.  Could you tell me which MSVC you're using, which C++ build more you're compiling with, whether you enable `/Zc:__cplusplus`, and how the execution fails?

---

## Comment 2

> Username: cfmileto  
> Created at: 2024-11-25 01:08:27 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2496479888  

In response to the programs request for input ("Enter a string followed by two unsigned integers. "), I entered the string “abc 123 654”. The program then terminates with an unhandled exception “Microsoft C++ exception: std::out_of_range at memory location 0x00000071F48FE0E0.” from the include file xstring (in C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.41.34120\include\xstring).  
  
   
  
I tried enabling “/Zc:__cplusplus” but got the same result.  
  
   
  
Below is a copy of info from the Visual Studio help page that I think identifies the C++ compiler build:  
  
   
  
“Microsoft Visual Studio Community 2022  
  
Version 17.11.5  
  
VisualStudio.17.Release/17.11.5+35327.3  
  
Microsoft .NET Framework  
  
Version 4.8.09037  
  
   
  
Installed Version: Community  
  
   
  
Visual C++ 2022   00482-90000-00000-AA095  
  
Microsoft Visual C++ 2022  
  
   
  
ASP.NET and Web Tools   17.11.231.19466  
  
ASP.NET and Web Tools  
  
   
  
Azure App Service Tools v3.0.0   17.11.231.19466  
  
Azure App Service Tools v3.0.0  
  
   
  
C# Tools   4.11.0-3.24460.3+5649376e0e5f5db3743a94a62b073f2cce4be5d9  
  
C# components used in the IDE. Depending on your project type and settings, a different version of the compiler may be used.  
  
   
  
GitHub Copilot   0.2.1657.32929  
  
GitHub Copilot is an AI pair programmer that helps you write code faster and with less work.  
  
   
  
Microsoft JVM Debugger   1.0  
  
Provides support for connecting the Visual Studio debugger to JDWP compatible Java Virtual Machines  
  
   
  
NuGet Package Manager   6.11.1  
  
NuGet Package Manager in Visual Studio. For more information about NuGet, visit https://docs.nuget.org/  
  
   
  
Test Adapter for Boost.Test   1.0  
  
Enables Visual Studio's testing tools with unit tests written for Boost.Test.  The use terms and Third Party Notices are available in the extension installation directory.  
  
   
  
Visual Basic Tools   4.11.0-3.24460.3+5649376e0e5f5db3743a94a62b073f2cce4be5d9  
  
Visual Basic components used in the IDE. Depending on your project type and settings, a different version of the compiler may be used.  
  
   
  
Visual C++ for Cross Platform Mobile Development (Android)   15.0.35103.136  
  
Visual C++ for Cross Platform Mobile Development (Android)  
  
   
  
Visual Studio IntelliCode   2.2  
  
AI-assisted development for Visual Studio.”  
  
   
  
Please let me know if you need additional info. Thank you for your assistance.  
  
   
  
Chris Mileto  
  
   
  
From: Zach Laine ***@***.***   
Sent: Sunday, November 24, 2024 2:38 PM  
To: boostorg/parser  
Cc: cfmileto; Author  
Subject: Re: [boostorg/parser] Example program parsing_into_a_class.cpp fails to execute correctly under MSVC. (Issue #206)  
  
   
  
Thanks for reporting this. Could you tell me which MSVC you're using, which C++ build more you're compiling with, whether you enable /Zc:__cplusplus, and how the execution fails?  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/parser/issues/206#issuecomment-2496196934> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/AIG4KVDVTKMTPA7V4CHJCV32CITITAVCNFSM6AAAAABSMP2WTSVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDIOJWGE4TMOJTGQ> .  
You are receiving this because you authored the thread.  <https://github.com/notifications/beacon/AIG4KVH63UUZSCQVS4AEXBD2CITITA5CNFSM6AAAAABSMP2WTSWGG33NNVSW45C7OR4XAZNMJFZXG5LFINXW23LFNZ2KUY3PNVWWK3TUL5UWJTUUZDYUM.gif> Message ID: ***@***.***>

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-11-25 04:52:37 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2496822866  

Thanks for the extra info!  Things are definitely supposed to work for that version of MSVC.  It would also help to know what it does -- that is, how exactly is it behaving badly?  Like, could you just run it and show the shell input+output when you do?

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-11-25 04:57:59 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2496838912  

Why I ask is that it seems the second parser is just wrong.  I wrote it as `constexpr auto uint_string = bp::uint_ >> bp::char_ >> bp::char_`, but it should probably be `constexpr auto uint_string = bp::uint_ >> +bp::char_`.  I just want to make sure that this is the actual problem you're seeing, and not something else (since there could be two problems!).

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-11-25 04:59:00 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2496841890  

One more thing -- the title says "fails to execute correctly under MSVC".  Does this mean that it works correctly on Clang or GCC, or were you just listing the compiler to add more info?

---

## Comment 6

> Username: tzlaine  
> Created at: 2024-11-25 23:46:36 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2499262483  

Ok, I pushed a commit that I think is the fix.  It's not MSVC-specific.  Please re-open if this did not fix the failure you saw.

---

## Comment 7

> Username: cfmileto  
> Created at: 2024-11-29 03:46:37 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2507041190  

Sorry, for my late response, I have been out of pocket for a while.  
  
   
  
With respect to the example program parsing_into_a_class.cpp, the problem I experienced was with the first parser in the example; namely  
  
   
  
constexpr auto string_uint_uint = bp::lexeme[+(bp::char_ - ' ')] >> bp::uint_ >> bp::uint_;  
  
   
  
I also compiled and executed the example under GCC (linking to the same Boost library compiled by Microsoft) and got a similar failure as compiling and executing under Microsoft.  
  
   
  
If I wrap the parser in the string_view directive (as follows  
  
   
  
constexpr auto string_uint_uint = bp::string_view[bp::lexeme[+(bp::char_ - ' ')] >> bp::uint_ >> bp::uint_];  
  
   
  
then the program executes correctly.  
  
   
  
The Boost library (1.87.beta) was successfully built using the standard command b2 toolset=msvc –build-type=complete stage.  
  
   
  
The following image is the Windows 10 shell generated from Microsoft’s Visual Studio IDE when the example program is invoked and input is entered:  
  
   
  
  
  
   
  
   
  
The following image is produced from the Visual Studio IDE when the example program fails to continue execution:  
  
   
  
  
  
   
  
Thanks for your assistance.  
  
   
  
   
  
From: Zach Laine ***@***.***   
Sent: Sunday, November 24, 2024 11:53 PM  
To: boostorg/parser  
Cc: cfmileto; Author  
Subject: Re: [boostorg/parser] Example program parsing_into_a_class.cpp fails to execute correctly under MSVC. (Issue #206)  
  
   
  
Thanks for the extra info! Things are definitely supposed to work for that version of MSVC. It would also help to know what it does -- that is, how exactly is it behaving badly? Like, could you just run it and show the shell input+output when you do?  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/parser/issues/206#issuecomment-2496822866> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/AIG4KVHVQ6IXLNVFLZHK7LT2CKUKZAVCNFSM6AAAAABSMP2WTSVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDIOJWHAZDEOBWGY> .  
You are receiving this because you authored the thread.  <https://github.com/notifications/beacon/AIG4KVH5JHO52PDH5H3X6532CKUKZA5CNFSM6AAAAABSMP2WTSWGG33NNVSW45C7OR4XAZNMJFZXG5LFINXW23LFNZ2KUY3PNVWWK3TUL5UWJTUU2J7FE.gif> Message ID: ***@***.***>

---

## Comment 8

> Username: tzlaine  
> Created at: 2024-11-29 21:37:38 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2508682767  

I'm able to get the program to compile and execute correctly on GCC using several different versions of GCC.  I still don't know exactly what problem you're seeing; could you share a terminal screenshot (or just copy/paste the session) that shows the misbehavior?

---

## Comment 9

> Username: cfmileto  
> Created at: 2024-11-29 22:30:33 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2508710085  

I sent you two screen shots in my previous email of the misbehavior, maybe they didn’t come through.  
  
   
  
When I run the example program via the Microsoft Visual Studio IDE, the following Windows 10 command shell opens up with the prompt “Enter a string followed by two unsigned integers.”, when I enter the string “abc 123 456” the program hangs with an exception that can be seen in the second screen shot below in a window of the IDE.  
  
   
  
First screen shot of Windows command shell:  
  
   
  
  
  
   
  
Second screen shot of IDE:  
  
   
  
   
  
  
  
   
  
From: Zach Laine ***@***.***   
Sent: Friday, November 29, 2024 4:38 PM  
To: boostorg/parser  
Cc: cfmileto; Author  
Subject: Re: [boostorg/parser] Example program parsing_into_a_class.cpp fails to execute correctly under MSVC. (Issue #206)  
  
   
  
I'm able to get the program to compile and execute correctly on GCC using several different versions of GCC. I still don't know exactly what problem you're seeing; could you share a terminal screenshot (or just copy/paste the session) that shows the misbehavior?  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/parser/issues/206#issuecomment-2508682767> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/AIG4KVASJR3BBD2HGFCFYN32DDNDPAVCNFSM6AAAAABSMP2WTSVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDKMBYGY4DENZWG4> .  
You are receiving this because you authored the thread.  <https://github.com/notifications/beacon/AIG4KVF6GFAOMCNR7TLHPZT2DDNDPA5CNFSM6AAAAABSMP2WTSWGG33NNVSW45C7OR4XAZNMJFZXG5LFINXW23LFNZ2KUY3PNVWWK3TUL5UWJTUVQ53A6.gif> Message ID: ***@***.***>

---

## Comment 10

> Username: tzlaine  
> Created at: 2024-11-29 22:42:25 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2508715448  

Ah!  I get you now.  Yeah, but that's because you told it to do that. :)  The example takes the first thing parsed, a `std::string`, and uses it and the next two things parsed (both integers) to construct a new `std::string`.  
  
In other words, to told the program to please construct a string like `s2`:  
  
```c++  
std::string s1("abc");  
  
std::string s2(s1, 123, 123);  
```  
  
And it faithfully followed your commands, but those indices are out of bounds.  If you wrote `abc 1 2` it would have worked fine, and constructed a string equal to `"bc"`.

---

## Comment 11

> Username: tzlaine  
> Created at: 2024-11-29 22:43:05 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2508715728  

Oh, and BTW, I *still* can't see any screenshots, in the emails I got, or here.  Not sure why.  Thanks for following up, though.

---

## Comment 12

> Username: cfmileto  
> Created at: 2024-11-29 23:27:06 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2508731487  

Ah! Works just like you said!  
  
   
  
As a novice to your library, it wasn’t intuitively obvious to me that the parser was matching the parsed tokens to a class constructor.  
  
   
  
I had assumed that the parser looked at the type of the variable to receive the parse in this case “string_from_parse” defined as std::string and would subsequently output the first token (a string) as is and then convert and append the uints to one instance of a string.  
  
   
  
Thanks so much for bearing with me. I look forward to using your library. I have used Spirit X3 in the past and have also encountered the same attribute generation problems that you mentioned you encountered with it that may have prompted you to develop your library.  
  
   
  
From: Zach Laine ***@***.***   
Sent: Friday, November 29, 2024 5:43 PM  
To: boostorg/parser  
Cc: cfmileto; Author  
Subject: Re: [boostorg/parser] Example program parsing_into_a_class.cpp fails to execute correctly under MSVC. (Issue #206)  
  
   
  
Ah! I get you now. Yeah, but that's because you told it to do that. :) The example takes the first thing parsed, a std::string, and uses it and the next two things parsed (both integers) to construct a new std::string.  
  
In other words, to told the program to please construct a string like s2:  
  
std::string s1("abc");  
   
std::string s2(s1, 123, 123);  
  
And it faithfully followed your commands, but those indices are out of bounds. If you wrote abc 1 2 it would have worked fine, and constructed a string equal to "bc".  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/parser/issues/206#issuecomment-2508715448> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/AIG4KVE3MPLQHLLZPIXJ55T2DDUWNAVCNFSM6AAAAABSMP2WTSVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDKMBYG4YTKNBUHA> .  
You are receiving this because you authored the thread.  <https://github.com/notifications/beacon/AIG4KVD6PI6KCHFOXQ5COU32DDUWNA5CNFSM6AAAAABSMP2WTSWGG33NNVSW45C7OR4XAZNMJFZXG5LFINXW23LFNZ2KUY3PNVWWK3TUL5UWJTUVQ723Q.gif> Message ID: ***@***.***>

---

## Comment 13

> Username: tzlaine  
> Created at: 2024-11-30 01:32:57 UTC  
> Url: https://github.com/boostorg/parser/issues/206#issuecomment-2508776755  

Sure thing!  The generation of a std::string from std::string, int, int, was actually the point of that particular example.  It's showing how Boost.Parser will try to find a ctor that matches some sequence of attributes.  That's not 100% clear from the example code, but the part of the docs where the example code is quote makes it clearer.
