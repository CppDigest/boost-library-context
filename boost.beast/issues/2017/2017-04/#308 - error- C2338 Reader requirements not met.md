# #308 - error: C2338 Reader requirements not met [Closed]

> Username: Lifeda92  
> Created at: 2017-04-19 19:00:38 UTC  
> Updated at: 2017-04-28 00:45:31 UTC  
> Closed at: 2017-04-28 00:45:31 UTC  
> Url: https://github.com/boostorg/beast/issues/308  

Hello, i tried use your library Beast on VSE 2015, boost v. 1.62. When i tried to compile example code HTTP GET, WebSocket (http://vinniefalco.github.io/beast/beast/example.html), i got an error: C2338 Reader requirements not met. How can i fix it? Help me, please, thx.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-19 19:44:18 UTC  
> Url: https://github.com/boostorg/beast/issues/308#issuecomment-295409083  

Which version of Beast are you using? You can tell by looking at `include/version.hpp`, or looking at the tip commit message.

---

## Comment 2

> Username: Lifeda92  
> Created at: 2017-04-19 20:07:07 UTC  
> Url: https://github.com/boostorg/beast/issues/308#issuecomment-295418327  

1.0.0-b32

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-19 20:24:49 UTC  
> Url: https://github.com/boostorg/beast/issues/308#issuecomment-295425501  

Please try **1.0.0-b34**, currently at the tip of the _master_ branch?

---

## Comment 4

> Username: Lifeda92  
> Created at: 2017-04-19 21:17:54 UTC  
> Url: https://github.com/boostorg/beast/issues/308#issuecomment-295447236  

Tried 1.0.0-b34. Now this error: C4996	'std::equal::_Unchecked_iterators::_Deprecate': Call to 'std::equal' with parameters that may be unsafe - this call relies on the caller to check that the passed values are correct. To disable this warning, use -D_SCL_SECURE_NO_WARNINGS. See documentation on how to use Visual C++ 'Checked Iterators'	project	c:\program files\microsoft visual studio 14.0\vc\include\xutility	2983

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-04-19 21:37:51 UTC  
> Url: https://github.com/boostorg/beast/issues/308#issuecomment-295454840  

I'm not sure why you're having so much trouble. Are you using CMake to build the Visual Studio project files (.vcxproj files)? What is your exact command line to invoke CMake? The build scripts that come with Beast set `_SCL_SECURE_NO_WARNINGS`, see:  
https://github.com/vinniefalco/Beast/blob/6d5547a32c50ec95832c4779311502555ab0ee1f/CMakeLists.txt#L10

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-04-28 00:45:31 UTC  
> Url: https://github.com/boostorg/beast/issues/308#issuecomment-297877306  

Are you still having problems? If so, feel free to reopen this issue. Or if you have a new problem, create a new issue - thanks!
