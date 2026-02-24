# #691 - Document the need to disable autolinking for header-only on msvc [Closed]

> Username: mjbrichards  
> Created at: 2022-03-17 09:48:00 UTC  
> Updated at: 2023-04-02 18:44:45 UTC  
> Closed at: 2023-04-02 18:44:45 UTC  
> Url: https://github.com/boostorg/json/issues/691  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Boost  
  
107800  
  
### Steps necessary to reproduce the problem  
  
I want to use the header-only version of boost/json, following the documentation at https://www.boost.org/doc/libs/1_75_0/libs/json/doc/html/json/quick_look.html:  
  
<html>  
<body>  
<!--StartFragment--><p style="text-align: left; font-size: 10pt; line-height: 1.15; color: rgb(0, 0, 0); font-family: sans-serif; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;">n order to link your program you will need to link with a built library. Alternatively you can use the header-only configuration simply by including this header file in any<span> </span><span class="emphasis"><em>one</em></span><span> </span>of your new or existing source files:</p><pre class="programlisting" style="overflow: auto; font-size: 9pt; display: block; margin: 1pc 62.8359px 0pc; padding: 0.5pc; border: 1px solid rgb(220, 220, 220); color: rgb(0, 0, 0); font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><span class="preprocessor" style="color: rgb(64, 32, 128);">#include</span> <span class="special" style="color: rgb(112, 112, 112);">&lt;</span><span class="identifier" style="color: rgb(0, 0, 0);">boost</span><span class="special" style="color: rgb(112, 112, 112);">/</span><span class="identifier" style="color: rgb(0, 0, 0);">json</span><span class="special" style="color: rgb(112, 112, 112);">/</span><span class="identifier" style="color: rgb(0, 0, 0);">src</span><span class="special" style="color: rgb(112, 112, 112);">.</span><span class="identifier" style="color: rgb(0, 0, 0);">hpp</span><span class="special" style="color: rgb(112, 112, 112);">&gt;</span>  
</pre><div class="note" style="font-size: 9pt; line-height: 1.2; display: block; margin: 1pc 62.8359px 0pc; padding: 0.5pc; border: 1px solid rgb(220, 220, 220); color: rgb(0, 0, 0); font-family: sans-serif; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;">  
  
  | Note  
-- | --  
Sample code and identifiers used throughout are written as if the following declarations are in effect:#include <boost/json.hpp> using namespace boost::json;  
  
</div><br class="Apple-interchange-newline"><!--EndFragment-->  
</body>  
</html>  
  
I am using the following simple code:  
  
#include <iostream>  
  
#include <boost/json.hpp>  
#include <boost/json/src.hpp>  
  
using namespace boost::json;  
  
int main()  
{  
    object MyObject;  
    MyObject["Hello"] = "World";  
    std::cout << serialize(MyObject) << "\n";  
}  
  
### All relevant compiler information  
  
Using Visual Studio 2022.  
Output is:  
  
Build started...  
1>------ Build started: Project: JsonTest, Configuration: Debug x64 ------  
1>JsonTest.cpp  
1>LINK : fatal error LNK1104: cannot open file 'libboost_json-vc143-mt-gd-x64-1_78.lib'  
1>Done building project "JsonTest.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-03-17 09:50:54 UTC  
> Url: https://github.com/boostorg/json/issues/691#issuecomment-1070683529  

I suspect, the issue is that Boost autolinking is enabled.

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-03-17 10:00:58 UTC  
> Url: https://github.com/boostorg/json/issues/691#issuecomment-1070711546  

Actually, try putting src.hpp include before the other one.

---

## Comment 3

> Username: mjbrichards  
> Created at: 2022-03-17 10:06:53 UTC  
> Updated at: 2022-03-17 10:09:05 UTC  
> Url: https://github.com/boostorg/json/issues/691#issuecomment-1070733753  

Thanks for your speedy response, @grisumbras. I had already tried changing the order of the #defines and it made no difference; but disabling autolinking worked immediately. Would it be possible to include that in the documentation where it refers to header-only inclusion? Thanks very much for your help.

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-03-18 06:03:11 UTC  
> Url: https://github.com/boostorg/json/issues/691#issuecomment-1072059936  

Good idea.
