# #2366 - Boost Beast: Recommendations for parsing URI in a secure manner [Closed]

> Username: WarrenN1  
> Created at: 2021-12-22 01:11:51 UTC  
> Updated at: 2022-06-14 17:40:05 UTC  
> Closed at: 2022-06-14 17:40:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2366  

I was wondering if there was a boost native way to parse a URI in a secure/safe manner? I am trying to avoid the common issue of implementing an URI parser myself to protect against malformed attacks when there is already a good method present. For example, parsing:  
  
https://example.com/food/buy.php?name=potato&quantity=10  
  
Bonus: Do you know any good ways to convert these into MYSQL injection protected queries? Sorry if these questions are basic, but I am self-taught and currently have no mentor so I am just winging it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-22 01:12:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2366#issuecomment-999204861  

Have a look here: https://github.com/CPPAlliance/url

---

## Comment 2

> Username: WarrenN1  
> Created at: 2021-12-22 02:41:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2366#issuecomment-999240363  

How should I perform the install? I don't see installation instructions on the README etc. Is this similar to the boost installation process?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-12-22 05:56:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2366#issuecomment-999307210  

That's a good question...

---

## Comment 4

> Username: WarrenN1  
> Created at: 2021-12-22 06:18:10 UTC  
> Updated at: 2021-12-22 06:18:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2366#issuecomment-999316153  

> That's a good question...   
  
Is the "..." meant to mean I missed something obvious in the docs or that you have doubts about the installation procedure?  
Again thank you for all your guidance.

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-12-22 11:06:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2366#issuecomment-999487616  

URL is a header-only library. Until it has been proposed and accepted for Boost, you can simply copy the header files into your project.

---

## Comment 6

> Username: alandefreitas  
> Created at: 2021-12-22 15:06:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2366#issuecomment-999643614  

> How should I perform the install? I don't see installation instructions on the README etc. Is this similar to the boost installation process?  
  
@WarrenN1 This might be a little confusing because Boost.URL is not accepted for Boost yet, but the build scripts and [CMakeLists.txt](https://github.com/CPPAlliance/url/blob/develop/CMakeLists.txt) are structured as if they were already part of the boost libraries.   
  
This means if you were cloning [the whole boost project](https://github.com/boostorg/boost) and building it with Boost.URL as part of it you would usually:  
  
```bash  
git clone https://github.com/boostorg/boost  
cd libs  
git clone https://github.com/CPPAlliance/url  
# build boost as usual  
```  
  
But if you already have boost installed and are already using something equivalent to:  
  
```cmake  
find_package(Boost)  
```  
  
you probably just want to use Boost.Url on top of it without messing with the Boost installation.  
  
In that case, Boost.Url's build scripts / [CMakeLists.txt](https://github.com/CPPAlliance/url/blob/develop/CMakeLists.txt) might fail if you try the usual install commands, to `find_package(url)`, or `add_subdirectory(url)` as an independent library.  The scripts might work, but it's best not to count on it.  
  
So if that's your case (likely), you can just bypass the build-scripts completely and only use the headers, which means you can just 1) copy the contents of the [include directory](https://github.com/CPPAlliance/url/tree/develop/include) to your project or 2) create an interface target for the header files with something like:  
  
```cmake  
add_library(boost_url INTERFACE)  
target_include_directories(boost_url INTERFACE /path/where/you/downloaded/url/include)  
# ...  
target_link_libraries(your_target PUBLIC boost_url)  
```

---

## Comment 7

> Username: WarrenN1  
> Created at: 2021-12-23 00:22:59 UTC  
> Updated at: 2021-12-23 01:31:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2366#issuecomment-999956643  

Hey I am now just dealing with a problem with running the doc examples, but I got the installation working. Thank you to everyone @vinniefalco  @alandefreitas @madmongo1. I have moved it to:  
https://github.com/CPPAlliance/url/issues/90  
because this is no longer really a beast issue. Thank you all again so much for your help. You all are amazing.
