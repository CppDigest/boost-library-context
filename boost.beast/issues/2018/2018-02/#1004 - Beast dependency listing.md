# #1004 - Beast dependency listing [Closed]

> Username: ayounes-nviso  
> Created at: 2018-02-02 09:48:35 UTC  
> Updated at: 2018-12-03 14:12:48 UTC  
> Closed at: 2018-12-03 14:12:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1004  

### Version of Beast: latest develop aka 153  
commit 02e0f0016430394ce78775c1ac38e775ff378fa0 (HEAD -> develop, origin/develop)  
  
### Steps necessary to reproduce the problem  
  
Try to use beast without full boost dependency  
  
### All relevant compiler information  
gcc version 7.2.0 (Ubuntu 7.2.0-8ubuntu3)   
Ubuntu 17.10  
  
Hi there,  
  
For various reasons I can't take all of boost (cross-platform development, cmake build system, etc)  
As boost.beast home page says that it only depends on boost.system and asio I gave it a try and the list of dependencies is much bigger than that (direct or indirect dependencies). I guess this is not really a bug but in information for you to update your home page or as a warning that the work to do that is higher than advertised.  
  
asio    config     functional  mpl       predef        static_assert    type_traits  
assert  core       intrusive   numeric/conversion   preprocessor  system  utility  
beast   date_time  move        optional  smart_ptr     throw_exception  
  
Thanks and Best regards,  
Abdel

---

## Comment 1

> Username: cmazakas  
> Created at: 2018-02-06 17:19:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-363496979  

> For various reasons I can't take all of boost (cross-platform development, cmake build system, etc)  
  
  
These almost sounds like a non-issue. You can easily use CMake with Boost and have cross-platform support.  
  
Mind you, if you still try to use Boost.Hana on msvc you'll have a bad time but simply avoid the libs that you can't use.  
  
If you need an example of a fine-grained CMakeLists.txt file for cross-platform Boost development, I can link you to some of my mine. The secret is to link against the individual libs you want to use and not the entirety of Boost.

---

## Comment 2

> Username: ayounes-nviso  
> Created at: 2018-02-06 17:32:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-363501085  

We don't have any issue using cmake and boost; that's fine and easy indeed:  
  
1) we created our own boost master repo that has all necessary git submodules for beast (we don't want to clone the full boost master repo (https://github.com/boostorg/boost)  
  
2) we wrote a simple CMakeLists.txt to compile boost.system and expose all necessary include paths. I pasted the code below. If there is an interest I can publish this git repo as well (maintained on bitbucket).  
  
set(LIB nv3dfi-boost)  
file(GLOB SRC system/src/*.cpp)  
add_library(${LIB} ${SRC})  
  
target_include_directories(${LIB}  
    # direct beast dependencies  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/type_traits/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/predef/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/config/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/assert/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/core/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/config/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/static_assert/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/utility/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/optional/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/smart_ptr/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/move/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/intrusive/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/throw_exception/include  
    # date_time dependency  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/functional/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/numeric/conversion/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/preprocessor/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/mpl/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/date_time/include  
    # advertised beast dependency  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/system/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/asio/include  
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/beast/include)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-06 19:44:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-363541883  

Beast only requires _linking_ against Boost.System. I really don't understand why you are wasting so much energy to avoid certain libs. Just add all of Boost as one dependency and call it a day.

---

## Comment 4

> Username: ayounes-nviso  
> Created at: 2018-02-07 08:43:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-363696974  

On 02/06/2018 08:44 PM, Vinnie Falco wrote:  
>  
> Beast only requires /linking/ against Boost.System. I really don't   
> understand why you are wasting so much energy to avoid certain libs.   
> Just add all of Boost as one dependency and call it a day.  
>  
  
  
Hi Vinnie,  
  
My intention is not to criticize the current situation, only to solve my   
particular use case. We deeply appreciate your software and the gift to   
the C++ community. Please disregard this report and close the ticket if   
this is not useful for you.  
  
I don't want to argue about that but in case you want an explanation, we   
are delivering our software through git and git submodules. The   
installation and compilation must be as simple as possible and as fast   
as possible so we only embed the mandatory 3rd parties and we try to   
minimize our dependencies. Our self made boost repo is only about 50M   
while the official boost repo is 1.6G when cloned.  
  
Thanks and best regards,  
Abdel

---

## Comment 5

> Username: pdimov  
> Created at: 2018-02-07 13:51:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-363774722  

https://pdimov.github.io/boostdep-report/develop/beast.html

---

## Comment 6

> Username: ayounes-nviso  
> Created at: 2018-02-07 15:10:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-363798787  

On Wed, Feb 7, 2018 at 2:51 PM, Peter Dimov <notifications@github.com>  
wrote:  
  
> https://pdimov.github.io/boostdep-report/develop/beast.html  
>  
  
Great tool and report, thanks!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-12-03 00:11:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-443555730  

Boost.System is now header-only (thanks Peter) so the next official release of Beast in Boost will not require linking anything at all.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-12-03 00:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-443555876  

You can see the latest dependencies in the Travis and/or Appveyor build scripts:  
https://github.com/boostorg/beast/blob/master/.travis.yml#L129  
https://github.com/boostorg/beast/blob/master/appveyor.yml#L24

---

## Comment 9

> Username: ayounes-nviso  
> Created at: 2018-12-03 14:12:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1004#issuecomment-443723189  

This is great news. Closing the ticket.
