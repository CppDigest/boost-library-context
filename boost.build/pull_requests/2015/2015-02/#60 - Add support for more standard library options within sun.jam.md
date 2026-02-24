# #60 Add support for more standard library options within sun.jam [Closed]

> Username: jzmaddock  
> Created at: 2015-02-26 13:16:17 UTC  
> Updated at: 2021-10-02 22:20:36 UTC  
> Closed at: 2015-03-03 19:11:28 UTC  
> Url: https://github.com/boostorg/build/pull/60  

The latest Oracle compiler has several std lib options, all of which work with Boost to some degree or another:  
- default C++03 - Rogue Wave  
- stlport C++ 03  
- Apache C++03  
- C++11 - a version of GNU Libstdc++  
  
This patch provides a means to switch between them.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2015-03-03 09:05:32 UTC  
> Url: https://github.com/boostorg/build/pull/60#issuecomment-76909693  

AMDG  
  
One minor issue I have with this is the naming.  
The existing option is called <stdlib>sun-stlport.  
I believe this is to avoid a conflict with  
stlport.jam.  I know that Boost.Build doesn't  
have direct support for stdcxx, right now,  
but do you know if the apache library can  
be used as a replacement standard library  
in general, so that this option could  
theoretically conflict with a hypothetical  
apache.jam?  
  
The other issue is that it seems somewhat  
non-intuitive that the standard library  
option should set the language version.  
Is there any way around this?  Does -std=c++03  
have any other side effects?  I know with  
gcc, the default is actually -std=gnu++NN,  
rather than -std=c++NN, for example.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-03-03 10:54:55 UTC  
> Url: https://github.com/boostorg/build/pull/60#issuecomment-76924783  

On 03/03/2015 09:05, swatanabe wrote:  
  
> AMDG  
>   
> One minor issue I have with this is the naming.  
> The existing option is called <stdlib>sun-stlport.  
> I believe this is to avoid a conflict with  
> stlport.jam. I know that Boost.Build doesn't  
> have direct support for stdcxx, right now,  
> but do you know if the apache library can  
> be used as a replacement standard library  
> in general, so that this option could  
> theoretically conflict with a hypothetical  
> apache.jam?  
  
I guess so, the apache library is the successor to the Rogue Wave   
library, but open source (http://stdcxx.apache.org/).  
  
However, the library development appears to be dead, and I've never   
heard of it being used with any compiler other than Oracle's.  
  
> The other issue is that it seems somewhat  
> non-intuitive that the standard library  
> option should set the language version.  
> Is there any way around this? Does -std=c++03  
> have any other side effects? I know with  
> gcc, the default is actually -std=gnu++NN,  
> rather than -std=c++NN, for example.  
  
It would be more accurate to call these binary-compatibility options   
rather than library options, but there is one standard library for each   
binary option: you absolutely cannot mix and match.  There are also no   
gnu++ options.  To summarise we have:  
  
C++03, sun object format, sun lib:  default.  
C++03, sun object format, STLPort, -library=-stlport4  
C++03, sun object format, Apache, -library=-stdcxx4  
C++03, GNU binary, GNU lib, -std=c++03  
C++11, GNU binary, GNU lib, -std=c++11  
  
There is also -std=sun-03 which is the same as one of the first 3 (not   
sure which).  And c++11 mode only works in GNU mode with GNU libstdc++3.  
  
The above 5 options are mutually incompatible, and -compat=, -library=   
and -std= options cannot be mixed and matched - in short their command   
line syntax is a bit of mess :-(  
  
John.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2015-03-03 16:25:22 UTC  
> Url: https://github.com/boostorg/build/pull/60#issuecomment-76978804  

AMDG  
  
On 03/03/2015 03:54 AM, jzmaddock wrote:  
  
> I guess so, the apache library is the successor to the Rogue Wave   
> library, but open source (http://stdcxx.apache.org/).  
>   
> However, the library development appears to be dead, and I've never   
> heard of it being used with any compiler other than Oracle's.  
  
All right.  In that case there shouldn't be a problem.  
  
> It would be more accurate to call these binary-compatibility options   
> rather than library options, but there is one standard library for each   
> binary option: you absolutely cannot mix and match.  There are also no   
> gnu++ options.  To summarise we have:  
>   
> C++03, sun object format, sun lib:  default.  
> C++03, sun object format, STLPort, -library=-stlport4  
> C++03, sun object format, Apache, -library=-stdcxx4  
> C++03, GNU binary, GNU lib, -std=c++03  
> C++11, GNU binary, GNU lib, -std=c++11  
>   
> There is also -std=sun-03 which is the same as one of the first 3 (not   
> sure which).  And c++11 mode only works in GNU mode with GNU libstdc++3.  
>   
> The above 5 options are mutually incompatible, and -compat=, -library=   
> and -std= options cannot be mixed and matched - in short their command   
> line syntax is a bit of mess :-(  
  
One more question.  If you provide _both_ -std=c++03  
and -std=c++11, is it a hard error, does it do something  
strange, or does it pick one in a predictable way?  
I'd rather have -std=c++03 and -std=c++11 use the same  
<stdlib> property and have some other way to distinguish  
them.  Hmmm.  There was talk a while back about a feature  
to control c++11/c++03.  Let me check on that.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-03-03 17:07:32 UTC  
> Url: https://github.com/boostorg/build/pull/60#issuecomment-76987844  

One more question. If you provide _both_ -std=c++03  
  
> and -std=c++11, is it a hard error, does it do something  
> strange, or does it pick one in a predictable way?  
> I'd rather have -std=c++03 and -std=c++11 use the same  
> <stdlib> property and have some other way to distinguish  
> them. Hmmm. There was talk a while back about a feature  
> to control c++11/c++03. Let me check on that.  
>   
> It works, and it picks the rightmost one on the command line.  
  
However you would have to be very careful not to break the   
stlport/apache/Cstd library options as you cannot mix the -std= command   
line option with either of the -library= or -compat= options (it's a   
hard error and bails right out).

---

## Comment 5

> Username: swatanabe  
> Created_at: 2015-03-03 17:28:03 UTC  
> Url: https://github.com/boostorg/build/pull/60#issuecomment-76992151  

AMDG  
  
On 03/03/2015 10:07 AM, jzmaddock wrote:  
  
> One more question. If you provide _both_ -std=c++03  
>   
> > and -std=c++11, is it a hard error, does it do something  
> > strange, or does it pick one in a predictable way?  
> > I'd rather have -std=c++03 and -std=c++11 use the same  
> > <stdlib> property and have some other way to distinguish  
> > them.  
> >   
> > It works, and it picks the rightmost one on the command line.  
>   
> However you would have to be very careful not to break the   
> stlport/apache/Cstd library options as you cannot mix the -std= command   
> line option with either of the -library= or -compat= options (it's a   
> hard error and bails right out).  
  
Okay, I propose that the stdlib feature  
should have a single value for libstdc++  
which should set -std=c++03.  
  
The result will be:  
  
<stdlib>native -> no options  
<stdlib>native + <cxxflags>-std=c++11 -> -std=c++11  
<stdlib>sun-stlport|apache -> -libary=...  
<stdlib>sun-stlport|apache + <cxxflags>-std=c++11 -> compiler error  
<stdlib>libstdc++ -> -std=c++03  
<stdlib>libstdc++ + <cxxflags>-std=c++11 -> -std=c++03 -std=c++11  
  -> -std=c++11  
  
which I think is sensible.  (Note: we don't have  
to do anything special to make sure that -std=c++11  
appears after -std=c++03.  It matches the default  
lexicographical sort order.)  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-03-03 18:40:48 UTC  
> Url: https://github.com/boostorg/build/pull/60#issuecomment-77006688  

OK, I've updated and tested the patch accordingly.  Now uses stdlib=gnu to select libstdc++3.  
  
Aside: I still think we need a standard conformance selection feature, it's a pain to use cxxflags as that doesn't work if you want to test multiple configurations.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2015-03-03 19:36:04 UTC  
> Url: https://github.com/boostorg/build/pull/60#issuecomment-77017611  

Thanks!

---
