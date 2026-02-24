# #46 - BOOST_SKIP_INSTALL_RULES [Closed]

> Username: BraHei  
> Created at: 2023-11-24 11:46:03 UTC  
> Updated at: 2023-12-21 23:48:32 UTC  
> Closed at: 2023-12-21 23:48:32 UTC  
> Url: https://github.com/boostorg/cmake/issues/46  

I am currently using boost with FetchContent and want to install boost along with the main project.  
  
This is currently not possible since BOOST_SKIP_INSTALL_RULES is set on OFF if boost is not the root project.  
  
Would it be feasible to check if the flag is defined and if not set it to OFF as such:  
  
```  
  if(NOT DEFINED BOOST_ENABLE_PYTHON)  
    set(BOOST_ENABLE_PYTHON OFF)  
  endif()  
  
  set(BUILD_TESTING OFF)  
  if(NOT DEFINED BOOST_SKIP_INSTALL_RULES )  
    set(BOOST_SKIP_INSTALL_RULES OFF)  
  endif()  
```  
  
If not, could that be elaborated? I couldn't find any documentation on this specifically.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-11-24 12:50:20 UTC  
> Url: https://github.com/boostorg/cmake/issues/46#issuecomment-1825633165  

> I am currently using boost with FetchContent and want to install boost along with the main project.  
  
What is your use case? It's generally undesirable for projects to install Boost distributions; this can easily lead to a broken system or one with more than one version of Boost, which leads to subtle and hard to detect errors.  
  
Do you really need to install Boost _headers_ along with your project? What is your project?

---

## Comment 2

> Username: BraHei  
> Created at: 2023-11-24 13:21:59 UTC  
> Updated at: 2023-11-24 13:34:04 UTC  
> Url: https://github.com/boostorg/cmake/issues/46#issuecomment-1825671074  

In order to distribute a SDK I want to provide as much dependencies as possible. The SDK is distributed either through a GIT repo or through debian packages.  
  
When the GIT repo is used, it is possible to create a custom installation dir (through CMAKE_INSTALL_PREFIX) with all dependecies included.  
  
I see your point with the multiple boost versions being an issue, this is why I want to explicitly make it possible to be able to install boost along with the SDK as this makes sure the right version is available.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-12-21 23:48:26 UTC  
> Url: https://github.com/boostorg/cmake/issues/46#issuecomment-1867059452  

Fixed by https://github.com/boostorg/cmake/pull/47.
