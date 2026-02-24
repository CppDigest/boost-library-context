# #885 - Visual C++ linker error but not with GCC [Closed]

> Username: luz-arreola  
> Created at: 2024-11-12 06:58:04 UTC  
> Updated at: 2024-11-18 18:33:39 UTC  
> Closed at: 2024-11-14 19:33:36 UTC  
> Url: https://github.com/boostorg/url/issues/885  

I have not had any problems using Boost.URL (v. 1.86) with **GCC**.  
  
Firstly, I've successfully used **url_view** with **GCC** as well as with **VC++** to parse and iterate URLs.  
  
Recently, I decided to modify URLs and this is when I ended up getting a linker error with VC++ but not with GCC.  Please note that the code compiles with VC++ but will not link, here is a snippet that will cause a link error:  
  
`url u("https:www.example.com:443/path/file.txt?id=42&name=John");`  
  
Linker error:  
  
`1>libboost_url-vc143-mt-x64-1_86.lib(url_base.obj) : error LNK2001: unresolved external symbol __std_count_trivial_1`  
  
**Note**: adding **libboost_url-vc143-mt-x64-1_86.lib** to the VC++ Linker/Input/Additional Dependencies does not make any difference.  
  
For completeness, this is the working code of how I add parameters to the url above:  
  
`u.encoded_params().append({ "gender", "male" });`  
`u.encoded_params().append({ "age", "30" });`

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-11-12 14:02:26 UTC  
> Updated at: 2024-11-12 14:03:00 UTC  
> Url: https://github.com/boostorg/url/issues/885#issuecomment-2470616096  

Updating to the latest Visual Studio version should resolve this. What's missing is an internal implementation detail of MSVC.

---

## Comment 2

> Username: luz-arreola  
> Created at: 2024-11-12 20:07:04 UTC  
> Updated at: 2024-11-12 20:08:24 UTC  
> Url: https://github.com/boostorg/url/issues/885#issuecomment-2471471801  

Firstly, thank you for your response and thank you and Vinnie for your efforts with this very useful library.  I updated my version of VS 2022 and it appears that I will need to rebuild boost along with many different libraries before I can tell you whether or not your suggestion resolved my issue with VC++, because I am now swamped with compiler errors :)  
  
Off topic, the single reason that I use VC++ is because of it's very nice IDE.  I then compile and build my work, test on Windows and when ready, I simply build for Linux.    
  
I think that I will spend the rest of the day looking at IDE's for Linux, because other than the IDE, I have no reason to deal with Microsoft.  I will look at the open sourced Linux IDEs and commercial ones next.  I do not need an advanced debugger, I only need an advanced IDE with the excellent editing features of Visual Studio.  I don't do any GUI work whatsoever and do mostly work with boost beast.  If you can recommend something, either open source or not, I'd appreciate it.  Thanks once again.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-11-12 20:43:08 UTC  
> Url: https://github.com/boostorg/url/issues/885#issuecomment-2471543713  

Well... the library should work on nearly all compilers. In your case, it seems like you built boost with a different compiler version, and now your project can't link the symbols from the other compiler. But everything should work if it's the same compiler because we test the library extensively in CI. In any case, there are good IDEs and compilers for both Linux and Windows. Visual Studio has improved CMake support over the recent years, and many people are using Visual Studio Code right now. CLion has great CMake integration and all kinds of static analysis.

---

## Comment 4

> Username: luz-arreola  
> Created at: 2024-11-14 19:33:14 UTC  
> Url: https://github.com/boostorg/url/issues/885#issuecomment-2477251998  

I am convinced that this is an issue with VC++ 2022 and not with Boost.URL  
  
The first problem that I described in my top post, I am able to **parse** but not **modify** URLs pertains the **VC++ 2022 17.1.6**.    
  
After upgrading to **17.12.0**, something changed with the compiler and too many libraries that use C++20 features and that I heavily rely on no longer compile/build using either the **17.10.1** or **17.12.0** VC++ updates.  
  
For now, I'll just use GCC since I have not had a single issues with any of the libraries that I use, including Boost.URL.

---

## Comment 5

> Username: luz-arreola  
> Created at: 2024-11-18 18:33:37 UTC  
> Url: https://github.com/boostorg/url/issues/885#issuecomment-2483821875  

Alan was correct.  His suggestion to update VC++ 2020 resolved my unresolved external issue.  After upgrading, I had new problems but those were solved by cleaning up various preprocessor directives no longer needed.  
  
Everything is properly working with Boost.URLs for me at this time with VC++ 2022 and GCC.  Thank you Alan and Vinney for your excellent quality libraries!
