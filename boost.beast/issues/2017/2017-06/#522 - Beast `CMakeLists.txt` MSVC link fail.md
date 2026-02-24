# #522 - Beast `CMakeLists.txt` MSVC link fail [Closed]

> Username: cdacamar  
> Created at: 2017-06-21 06:05:15 UTC  
> Updated at: 2017-06-22 18:12:30 UTC  
> Closed at: 2017-06-22 18:12:30 UTC  
> Url: https://github.com/boostorg/beast/issues/522  

@ 6fab138150e6f61736a158c34c043a13d18e2f2a  
  
I'm not 100% sure `link_directories` should go away.  I had a link fail on MSVC 15.2 because `system`  has several other libraries it tries to link in. Normally this isn't a problem if you download and install the boost libraries but since I built the boost libraries on a separate drive the link step didn't know where to find things like `libboost_date_time.lib`. The quick solution:  
```cmake  
link_directories(${Boost_LIBRARY_DIR_RELEASE})  
link_directories(${Boost_LIBRARY_DIR_DEBUG})  
```  
Why both? They might be disjoint directories.  
  
Additionally this _could_ potentially be fixed within `find_package` but that might be outside the scope of Beast :wink:

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-22 00:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/522#issuecomment-310244308  

By the way the code I removed was wrong:  
```  
link_directories(${Boost_LIBRARY_DIR})  
```  
  
It should be `Boost_LIBRARY_DIRS`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-22 01:47:52 UTC  
> Updated at: 2017-06-22 01:48:08 UTC  
> Url: https://github.com/boostorg/beast/issues/522#issuecomment-310252728  

I'm adding  
```  
link_directories(${Boost_LIBRARY_DIRS})  
```  
  
This results in the following Additional Library Directories:  
```  
$(BOOST_ROOT)/stage/lib  
$(BOOST_ROOT)/stage/lib/$(Configuration)  
```  
  
That should cover the case you mentioned

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-22 18:12:30 UTC  
> Url: https://github.com/boostorg/beast/issues/522#issuecomment-310459528  

This was merged to version **64**  
https://github.com/vinniefalco/Beast/commit/8449697a2c225688a9cf8e4fcf01206d56903b85  
  
If its still a problem feel free to re-open the issue or to create a new one, thanks!
