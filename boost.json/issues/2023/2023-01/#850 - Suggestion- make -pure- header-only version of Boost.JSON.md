# #850 - Suggestion: make "pure" header-only version of Boost.JSON [Closed]

> Username: fsmoke  
> Created at: 2023-01-30 12:06:46 UTC  
> Updated at: 2024-05-06 06:52:45 UTC  
> Closed at: 2024-05-06 06:52:45 UTC  
> Url: https://github.com/boostorg/json/issues/850  

We have big product with dozens projects inside. Many of these projects use common headers, but we can't use boost json inside of theese headers, cos many developers will be enforced to include src.hpp or add link to boost.json library.   
  
It's very undesirable, and uncomfortable.  
  
My suggestion: add some macro like BOOST_JSON_PURE_HEADER_ONLY and if it turned on before **#include <boost/json.hpp>** - include **src.hpp** too. As addition - inside implementation files mark all symbols as **inline**, to avoid symbol duplication due linking process  
  
PS  
Boost version 1.80

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-01-30 14:21:24 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408709197  

src.hpp is meant to be included exactly once in a .cpp file

---

## Comment 2

> Username: fsmoke  
> Created at: 2023-01-30 14:52:17 UTC  
> Updated at: 2023-01-30 14:55:31 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408762925  

> to be included exactly once in a .cpp file  
  
i know it. But it's very uncomfortable. It means if i use boost json inside common header(NOT cpp) - ALL developers of ALL projects enforced to include src.hpp in cpp inside there projects - otherwise build of these projects just will be failed.   
  
i.e. your way is undesirable - it enforces everyone keep in mind to include src.hpp everywhere  
  
PS  
Now we use boost.property_tree inside common headers to parse json - cos property_tree pure header-only library.... we want use boost.json as only one reference library for json parsing and writing everywhere inside our projects - but we can't do this due to the fact that it's not real headeronly  
  
PS2  
Yes src.hpp must be included once inside one project - but we have hundreds projects with different build engines and etc and different developers

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-01-30 14:58:49 UTC  
> Updated at: 2023-01-30 14:59:10 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408777121  

> we have hundreds projects with different build engines and etc and different developers  
  
For each of your hundreds of projects:  
  
1. Create a new file boost_json_lib.cpp  
2. Inside that file put the following line:  
```  
#include <boost/json/src.hpp>  
```  
3. Add boost_json_lib.cpp to the list of .cpp files used to build your project  
  
Why is this difficult?

---

## Comment 4

> Username: fsmoke  
> Created at: 2023-01-30 15:15:49 UTC  
> Updated at: 2023-01-30 15:19:05 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408807439  

> Why is this difficult?  
  
It's difficult when you manage big product - you must notify many developers who use common header(one of hundred common headers). But if i include boost json inside another common header - i again must notify other developers. Moreover i must keep in mind who use one or the other common header (but i really may dont know about this information) - it's hell!!  
  
Plus developers may not be aware of of boost.json specifics - cos they use my wrappers - so searching for failed build reason may take whole days. They may not know at all that I use boost json, they may not even know what is boost json.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-01-30 15:30:50 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408833082  

If someone is using your library and you add JSON to it then you can just list Boost::json as a dependency in the CMakeLists.txt, and then it will automatically be picked up by anyone linking to your library.

---

## Comment 6

> Username: fsmoke  
> Created at: 2023-01-30 15:47:03 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408859229  

> CMakeLists  
  
we don't use cmake(sometimes use but in most cases no), moreover some developer groups use different build engines(msbiuld(yes for linux too), old makefiles and so on)  
  
PS  
i can't understand why you defend your point of view so critically - instead of thinking about my suggestion - my example is real problem from real life. Even giant boost.asio - became pure headeronly. Anyway i suggesting just to ADD pure headeronly variant of using library, but not rewrite anything or cut any functionality.

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-01-30 15:59:21 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408877209  

Can you explain how using a macro is simpler than adding a line in a file?

---

## Comment 8

> Username: fsmoke  
> Created at: 2023-01-30 16:20:33 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408928382  

> Can you explain how using a macro is simpler than adding a line in a file?  
  
Because macro will be defined once - once for all projects, and all future projects....sorry... you dont undestand what is headeronly idiom??  
  
for example i have common header in common directory (common.hpp)  
  
inside common.hpp:  
  
```  
#define BOOST_JSON_PURE_HEADER_ONLY //this is writed once in one common header!!!!   
#include <boost/json.hpp>  
...  
class some_wrapper_using_boost_json  
{  
....  
//here methods which use boost.json library to parse something, for example  
....  
}  
```  
  
then this header begin use 50 different developers from different points of planet - they use my class **some_wrapper_using_boost_json** ofcourse but not boost.json and even don't know about boost.json. But for now boost.json not headeronly and i enforced to asking every from 50 developer(with different skills, who using different 3dparty frameworks may be(not boost at all)) to add src.hpp or library dependency - it's looks like bad joke. And tomorrow another developer will be use my common.hpp - and i must keep in mind every developer.... or write dozens "tips and tricks" for every similar case...it's very frustrate

---

## Comment 9

> Username: grisumbras  
> Created at: 2023-01-30 16:24:47 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408935366  

So, making sure that every .cpp file includes this specific file is simpler than making sure one .cpp file includes src.hpp?

---

## Comment 10

> Username: grisumbras  
> Created at: 2023-01-30 16:38:11 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408955320  

Actually it's worse than that. The file with the macro would have to be included _before any file from Boost.JSON_. This setup screams "potential ODR violation".

---

## Comment 11

> Username: fsmoke  
> Created at: 2023-01-30 16:40:55 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408958868  

> So, making sure that every .cpp file includes this specific file is simpler than making sure one .cpp file includes src.hpp?  
  
my common.hpp may include many other dependencies  
  
```  
#include <boost/dll.hpp>  
#include <boost/asio.hpp>  
  
#define BOOST_JSON_PURE_HEADER_ONLY   
#include <boost/json.hpp>  
  
#include <other_common.hpp>  
#include <our_super_api.hpp>  
.....  
.....  
```  
and every developer need include just several common includes instead of hundreds headers inside his project   
  
it's widespread practice - don't you know about it?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2023-01-30 17:00:16 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408984985  

> you dont undestand what is headeronly idiom??  
  
I understand the header-only idiom, but we are trying to move away from header-only libraries because they increase compile times. I think it is reasonable to expect that users will know how to manage their project in terms of using dependencies.

---

## Comment 13

> Username: fsmoke  
> Created at: 2023-01-30 17:01:54 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408987344  

> This setup screams "potential ODR violation".  
  
same thing you ev got now if accidentally include src.hpp twice - no difference. But in my case we save the developer from garbage work.  
  
in practice all common libraries(or our common.hpp) often included in precompiled header once for whole project - chance getting ODR violation decreases to zero

---

## Comment 14

> Username: fsmoke  
> Created at: 2023-01-30 17:04:45 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1408991699  

> but we are trying to move away from header-only libraries because they increase compile times  
  
1)we use precompiled header - to decrease compile time  
2)there is many business models of developing - cut off one of them - just wrong

---

## Comment 15

> Username: grisumbras  
> Created at: 2023-01-30 20:21:23 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1409279498  

It doesn't matter if the devs _need_ to include a certain magic header. What matters is if they include a header from Boost.JSON before the magic header (which they can, can't they?), you have an ODR violation. How will that ODR violation manifest? Probably as a linking error. In worse cases, as a hard to fix bug.  
  
How will you prevent that? I assume, with some written guideline. So, my question is why can't the written guideline instead of being "never include anything from boost/json before the magic header" be "in your main.cpp include boost/json/src.hpp"?

---

## Comment 16

> Username: fsmoke  
> Created at: 2023-01-31 08:26:18 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1409952257  

> you have an ODR violation  
  
first of all: if twice include in single file - here header guard may help  
  
but although your case is extremely fantastic - let's think...  
  
I see two variants keep code from ODR violations even with headeronly implementation  
  
1) add some option to boost build - if it turned on boost.json will be headeronly - undesireble, cos we depends on concret boost build  
2) just add additional namespace - for example boost::json::headeronly::array, boost::json::headeronly::object and etc. As addition to reduce header parsing time you can add macro something like BOOST_JSON_HEADERONLY_NS. This variant more preffered - cos we dividing all symbols to headeronly and not headeronly to avoid ODR violations.

---

## Comment 17

> Username: grisumbras  
> Created at: 2023-01-31 09:07:54 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1410001061  

> first of all: if twice include in single file - here header guard may help  
  
That's actually an additional problem for your setup.  
  
```c++  
// json/value.hpp  
#infdef BOOST_JSON_VALUE_HPP  
#define BOOST_JSON_VALUE_HPP  
...  
#endif // BOOST_JSON_VALUE_HPP  
  
// your wrapper .hpp  
#define BOOST_JSON_HEADER_ONLY  
#include <boost/json/value.hpp>  
...  
  
// user's .cpp file  
#include <boost/json/value.hpp>  
#include <your/wrapper.hpp>  
```  
  
User's .cpp file now includes `value.hpp` in a non-header only mode, it _does not_ include `value.hpp` in header-only mode. You still have potential ODR violations, when linking with other TUs.   
  
Using inline namespace for header-only does not solve anything. You still have headers that are influenced by the order of inclusion, **which is always a terrible idea**. You will have some TUs linking to `boost::json::hdr_only::X` and others to `boost::json::X`.  
  
I still don't see how the issues with your setup could be fixed without a written guideline for devs.

---

## Comment 18

> Username: fsmoke  
> Created at: 2023-01-31 09:40:28 UTC  
> Updated at: 2023-01-31 14:50:25 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-1410046567  

> User's .cpp file now includes `value.hpp` in a non-header only mode, it _does not_ include `value.hpp` in header-only mode. You still have potential ODR violations, when linking with other TUs.  
  
In case with additional namespace - no ODR violations  
  
> You still have headers that are influenced by the order of inclusion  
  
NO!! Because macro just turn on namespace "headeronly" - you could rewrite header guard to handle this situation  
  
something like this:  
  
```  
#ifndef BOOST_JSON_HPP  
#define BOOST_JSON_HPP  
  
namespace boost  
{  
	namespace json  
	{  
		//non headeronly classes includes here  
	}  
}  
#endif  
  
#ifdef BOOST_JSON_HEADERONLY_NS  
  
#ifndef BOOST_JSON_HEADER_ONLY_HPP  
#define BOOST_JSON_HEADER_ONLY_HPP  
  
#define BOOST_JSON_INLINE inline  
namespace boost  
{  
	namespace json  
	{  
		namespace headeronly  
		{  
			//headeronly classes includes here  
			.....  
			#include <boost/src.hpp>  
		}  
	}  
}  
#undef BOOST_JSON_INLINE  
#endif  
  
#endif  
```  
  
and inside definition headers   
  
```  
#ifndef BOOST_JSON_INLINE  
#define BOOST_JSON_INLINE  
#endif  
....  
BOOST_JSON_INLINE  
storage_ptr  
value::  
destroy() noexcept  
{  
    switch(kind())  
    {  
....  
```  
  
**Now you can include <boost/json.hpp> multiple times in every order!**  
  
```  
//this way  
<boost/json.hpp>  
#define BOOST_JSON_HEADERONLY_NS  
<boost/json.hpp>  
  
//or this way  
#define BOOST_JSON_HEADERONLY_NS  
<boost/json.hpp>  
#undef BOOST_JSON_HEADERONLY_NS  
<boost/json.hpp>  
```  
  
**and in different TUs** - no matter. Now we have two different separated implementations in different namespaces - no problems here  
  
**NO  guideline for devs needed** at all - i'll use header only version, and side developers can use every version of boost.json - No conflicts and ODR violations  
  
PS  
I wrote example code in browser - may be inaccuracies

---

## Comment 19

> Username: nigels-com  
> Created at: 2024-05-05 23:10:09 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-2094987876  

@fsmoke I'm a bit sympathetic that precompiled headers are indeed a bit of paradigm for Windows, but not so much of a thing on other platforms.  We're cross platform Windows and Linux, where linking the boost json library _statically_ works well in our cmake-centric setup.  It's not so natural from the Linux perspective to put this complexity in the pre-processing.

---

## Comment 20

> Username: grisumbras  
> Created at: 2024-05-06 06:51:30 UTC  
> Updated at: 2024-05-06 06:52:00 UTC  
> Url: https://github.com/boostorg/json/issues/850#issuecomment-2095304614  

I am closing this. Contrary to OP's opinion, making this "macro makes the library header-only" is a significant refactoring task, and it will still be a source of constant "why do I have linking errors" issues.  
  
Furthermore, header-only used for simplified deployment is a way of the past. In this age you should just use a package manager. Particularly if you have thousands of projects.
