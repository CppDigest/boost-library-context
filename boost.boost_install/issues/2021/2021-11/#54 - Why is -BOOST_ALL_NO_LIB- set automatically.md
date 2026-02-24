# #54 - Why is "BOOST_ALL_NO_LIB" set automatically? [Closed]

> Username: Leon0402  
> Created at: 2021-11-02 16:40:35 UTC  
> Updated at: 2022-07-11 11:00:41 UTC  
> Closed at: 2022-07-11 11:00:41 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54  

Hi,   
  
I found out that in`boost_headers-config.cmake` the Target `Boost::headers` sets `BOOST_ALL_NO_LIB`:   
  
```  
set_target_properties(Boost::headers PROPERTIES  
  INTERFACE_INCLUDE_DIRECTORIES "${_BOOST_INCLUDEDIR}"  
  INTERFACE_COMPILE_DEFINITIONS "BOOST_ALL_NO_LIB"  
)  
```  
  
Why is it disabled and how can I enable it?   
  
What's the purpose of the Targets `Boost::disable_autolinking` and `Boost::dynamic_linking` if Boost by default already disabled autolinking?   
  
Also, I believe this is a difference in regard to https://cmake.org/cmake/help/latest/module/FindBoost.html which afaik doesn't disable it by default.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-02 16:54:22 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-957942016  

There is no reason to use autolinking when using CMake; you should link to the proper CMake targets.

---

## Comment 2

> Username: Leon0402  
> Created at: 2021-11-02 17:12:22 UTC  
> Updated at: 2021-11-02 17:16:35 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-957957540  

Thanks for the quick answer!   
  
Multiple questions here:   
- Are the above targets needed then? They don't seem to have any use case no more  
- Can this be somewhere documented? -> Personally I used the above FindBoost.html for documentation purposes, which doesn't say a word about this (also not in the section at the very bottom which covers BoostConfig)  
- Is there some list where I can find out what boost (header) library needs what CMake Targets?   
  
For example I use Boost Uuid and apparently on Windows it needs bcrypt https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/detail/random_provider_bcrypt.ipp#L21.   
I don't know the CMake Target here, I'm not sure if there is any at all, it's a Windows Library.   
So my workaround here is currently to set `BOOST_UUID_FORCE_AUTO_LINK` to enable dynamic linking for this library.  
  
Currently it feels a little bit wrong that I have to explicitly mention all the dependencies of Boost even though I don't use them directly in my code. This seems somewhat against what cmake tries to archieves in my opinion.   
Would it be an option to have instead of Boost::Headers a target for each Boost library (even if it's header only) and add dependencies to those?   
This is even more valid in the case above where I (presumably) need to add platform dependent code to only link bcrypt on Windows. This makes my code more complicated than it has to be.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-11-02 17:47:52 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-957985995  

The targets Boost::disable_autolinking and Boost::dynamic_linking don't do anything and are only defined for compatibility with FindBoost.  
  
I'm not sure that "I have to explicitly mention all the dependencies of Boost" is correct. The CMake config file generation is smart enough to pick up dependencies; e.g. in the Iostreams case, when built with zlib support, the CMake config does link Boost::iostreams to libz. (Although I notice that Boost.Atomic sometimes links to synchronization.lib, but this is not reflected in its dependencies, so the CMake config file misses it.)  
  
UUID however is a header-only library, and for historical reasons, we don't have targets for these in either FindBoost or the generated CMake config files, so there's nothing to attach the bcrypt dependency to. Autolinking has been hiding the problem, but I don't think it's the correct solution here. (We - Boost - are to this day not entirely clear on whether BOOST_ALL_NO_LIB should control autolinking to system libraries; it's original purpose is to control autolinking to Boost libraries, but the autolinking infrastructure then got (ab)used for linking to system libs.)  
  
I'll create an issue in UUID where we can discuss this problem.

---

## Comment 4

> Username: Leon0402  
> Created at: 2021-11-02 18:02:28 UTC  
> Updated at: 2021-11-02 18:03:49 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-957996939  

> The targets Boost::disable_autolinking and Boost::dynamic_linking don't do anything and are only defined for compatibility with FindBoost.  
  
FindBoost, as far as I know, doesn't set `BOOST_ALL_NO_LIB`. So I'm not sure what compatibility is archieved here? At least in the case of  `Boost::dynamic_linking ` it will break anyway, won't it? (In a more difficult way to understand, because you don't get the error "Target does not exists", but some possibly cryptic compiler errors)  
Because previously this would lead to dynamic linking being on. Maybe even without it (as in the case of Boost Uuid).   
  
> UUID however is a header-only library, and for historical reasons, we don't have targets for these in either FindBoost or the generated CMake config files, so there's nothing to attach the bcrypt dependency to  
  
Can we change this?  
  
I believe there is a similar issue with Boost.Process for example, which requires Boost::Filesystem, but as it's header only too, doesn't automatically link it.   
(I haven't actually checked the source code of Boost.Process, but in my project the build failed without filesystem. And if I'm not completely mistaken I didn't use anything except Boost.Process)  
  
In my opinion correct linking of system libraries is the important issue here. But  linking all boost libraries dependencies as well would be very good as well. Because as a user of say Boost.Process I have no idea that it internally used filesystem. Based on the compiler warnings, I probably then can guess what boost libraries I need to make it work (at least I can as someone who is familiar with cmake). But it's basically just extra work and everytime I see the `target_linked_libraries`I will have to rethink if the dependencies are really correct, because I do not explicitly use it in my code and therefore wonder if it's a left over or something. I don't think it ads a lot of benefit, but really just has drawbacks.   
  
We could add (still header-only) cmake targets for all header libraries of boost and add these dependencies (boost as well as system dependenices) as `INTERFACE` dependency to it. The `Boost::headers` could coexist for compatbility reasons.   
What you think?

---

## Comment 5

> Username: pdimov  
> Created at: 2021-11-02 18:09:27 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-958002284  

> So I'm not sure what compatibility is archieved here?  
  
CMake projects that link to Boost::disable_autolinking don't break.  
  
> We could add (still header-only) cmake targets for all header libraries of boost  
  
We can't. The CMake targets are automatically generated by the b2 targets, and header-only libraries don't have b2 targets. Maybe we could employ some b2 trickery to create "fake" b2 targets for the header-only libraries having this problem, and then traverse those in the CMake config generation part, but I'm not sure I'm enough of a b2 expert to pull this off.  
  
The most straightforward solution is to just create dummy compiled libraries for those header-only libraries that link to compiled ones, such as UUID and Process.

---

## Comment 6

> Username: Leon0402  
> Created at: 2021-11-02 18:13:32 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-958005314  

> The most straightforward solution is to just create dummy compiled libraries for those header-only libraries that link to compiled ones, such as UUID and Process.  
  
Alright! Let's see what maintainers of UUID say about the idea.

---

## Comment 7

> Username: pdimov  
> Created at: 2021-11-02 18:27:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-958020050  

I sent an e-mail to the developer list as well.

---

## Comment 8

> Username: Leon0402  
> Created at: 2021-11-02 18:58:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-958067009  

Alright, thanks! Let me know if there is any response

---

## Comment 9

> Username: pdimov  
> Created at: 2021-11-03 20:22:49 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-959962337  

I [removed](https://github.com/boostorg/boost_install/commit/258f7866c1cb1e4d1b3aebd0c4db594d373c686c) the automatic setting of `BOOST_ALL_NO_LIB` by all `Boost::` targets, and [reactivated the `Boost::disable_autolinking`, `Boost::diagnostic_definitions`](https://github.com/boostorg/boost_install/commit/1dfb9c2872231a75b19d4101df3fbb471dc1da84), and [`Boost::dynamic_linking`](https://github.com/boostorg/boost_install/commit/1e63a0a86b427dbb906a5c07f95804b5ae14c060) targets.  
  
This will go into the 1.78 beta, which is due in a week.

---

## Comment 10

> Username: Leon0402  
> Created at: 2021-11-05 13:18:23 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-961888090  

Thanks for the update!   
  
Is it planned to develop a cmake target based approach as said in the other project? So in the future autolinking can be removed again?   
  
Do I understand the following correct:   
- Dynamic Linking is in general only down for Windows (or only for msvc?) -> Is thery any harm in having dynamic linking enabled on other platforms / compilers as `Boost::disable_autolinking` only sets the macro for WIN32  
- `Boost::dynamic_linking` is not used anymore, so the macro `BOOST_ALL_DYN_LINK` does not exist anymore? -> It seems like `Boost::dynamic_linking`  does not set anymore this macro. -> So basically all libs will enable dynamic linking except when `DBOOST_ALL_NO_LIB` is set?

---

## Comment 11

> Username: pdimov  
> Created at: 2021-11-05 14:14:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-961930026  

> Boost::dynamic_linking is not used anymore, so the macro BOOST_ALL_DYN_LINK does not exist anymore? -> It seems like Boost::dynamic_linking does not set anymore this macro.  
  
I don't understand what you are asking. It sounds to me like you're saying that Boost::dynamic_linking used to do something and no longer does, but it's exactly the other way around. It used to do nothing and now defines BOOST_ALL_DYN_LINK.

---

## Comment 12

> Username: Leon0402  
> Created at: 2021-11-05 14:26:16 UTC  
> Url: https://github.com/boostorg/boost_install/issues/54#issuecomment-961938981  

Oh sorry, I didn't saw that there were two commits and didn't noticed that you also actually referenced both in your post above. I just saw the first one and was therefore confused.   
  
Now everything is clear
