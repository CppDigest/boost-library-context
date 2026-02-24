# #1270 - boost::beast::http::request causes BOOST_STATIC_ASSERT to fail [Closed]

> Username: idontknowwhatiamdoinghelpme  
> Created at: 2018-10-18 06:42:28 UTC  
> Updated at: 2018-11-30 14:18:54 UTC  
> Closed at: 2018-10-20 20:21:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1270  

**Version of Beast: 181  
compiled with Visual Studio 2015 Professional, v140**  
  
We want to use Beast for one of our projects so we updated Boost to 168 and included Beast 181. Everything is working fine except the **boost::beast::http::request**.  
  
When using the boost::beast::http::request a **boost_static_assert** fails and prevents the program from compiling sucessfully. The assertion is located in **boost\intrusive\detail\parent_from_member.hpp** line 52.  
The assertion itself:  
`BOOST_STATIC_ASSERT( sizeof(caster) == sizeof(int) );`  
  
We invested quite some time now but we simply can't figure out what causes the problem. This doesnt have to be an issue with boost or beast, maybe some of our project settings is causing this issue but as I already said we don't know what it is.  
  
We have already used the boost::beast::http::request successfully in a prototype project. We were also able to reproduce the issue in the prototype. When adding the parameter "/vmg" to the project settings in Visual Studio (C/C++ - CommandLine - Additional options) a C2338 happens as well. Removing the parameter didnt fix the issue on our main project sadly.  
  
Additional output:  
`\boost/intrusive/detail/parent_from_member.hpp(52): error C2338: sizeof(caster) == sizeof(int)  
boost/intrusive/detail/parent_from_member.hpp(98): note: see reference to function template instantiation 'ptrdiff_t boost::intrusive::detail::offset_from_pointer_to_member<boost::beast::http::basic_fields<std::allocator<char>>::value_type,Member>(const Member boost::beast::http::basic_fields<std::allocator<char>>::value_type::* )' being compiled  
`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-18 11:42:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1270#issuecomment-430976907  

Phew...that is something! Do the tests and examples compile successfully with VS2015 v140?  
  
@igaztanaga any ideas?

---

## Comment 2

> Username: idontknowwhatiamdoinghelpme  
> Created at: 2018-10-18 12:35:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1270#issuecomment-430991381  

Everything compiled succesfully with VS2015 v140. I did it on my local PC and checked https://www.boost.org/development/testing.html as well

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-10-18 13:39:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1270#issuecomment-431012060  

The cause is indeed `/vmg` flag https://godbolt.org/z/xIYba6 and it is exactly what [the assert comments say](https://github.com/boostorg/intrusive/blob/2ab3421b17009adac5b1f7a688b7962cf4709495/include/boost/intrusive/detail/parent_from_member.hpp#L49-L52).

---

## Comment 4

> Username: igaztanaga  
> Created at: 2018-10-18 19:56:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1270#issuecomment-431141080  

Pointer to member representations in MSVC is a mess. I haven't found a way to implement parent_from_member when the representation is bigger than an offset.  
  
A way to workaround this is to use base_hooks instead of member hooks in "boost::beast::http::basic_fields".

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-10-19 00:46:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1270#issuecomment-431209323  

I am attempting to use the base hooks...

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-10-20 00:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1270#issuecomment-431534417  

I changed `basic_fields` to use the base hooks instead, this problem should be resolved once it is merged - thanks for the report!

---

## Comment 7

> Username: idontknowwhatiamdoinghelpme  
> Created at: 2018-10-21 14:23:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1270#issuecomment-431673118  

Thanks for the fix. We are really looking forward to use beast in our project!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-11-30 14:18:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1270#issuecomment-443216872  

> We invested quite some time now  
  
For future reference, I suggest that you (and everyone else reading this!) open an issue sooner rather than later. I know the library and boost much better than most since I wrote Beast of course, so what takes you a week might only take me a few minutes. Especially when an assert goes off, best thing to do is just open an issue right away and then continue trying to diagnose it. If I can save people hours or days of headache, it is worth it!
