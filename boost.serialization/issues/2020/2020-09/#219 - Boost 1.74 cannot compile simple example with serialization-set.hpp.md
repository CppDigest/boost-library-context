# #219 - Boost 1.74 cannot compile simple example with serialization/set.hpp [Closed]

> Username: fcooper8472  
> Created at: 2020-09-08 16:48:55 UTC  
> Updated at: 2023-09-29 11:06:39 UTC  
> Closed at: 2023-09-03 19:24:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/219  

[Minimal failing example.](https://gcc.godbolt.org/z/3zesE9)  
  
Including `boost/serialization/set.hpp` does not compile:  
  
```cpp  
#include <boost/serialization/set.hpp>  
  
int main() {  
    return 0;  
}  
```  
  
the following error is produced:  
  
```  
/celibs/boost_1_74_0/boost/serialization/set.hpp: In function 'void boost::serialization::load_set_collection(Archive&, Container&)':  
  
/celibs/boost_1_74_0/boost/serialization/set.hpp:40:33: error: 'library_version_type' in namespace 'boost::serialization' does not name a type; did you mean 'item_version_type'?  
  
   40 |     const boost::serialization::library_version_type library_version(  
  
      |                                 ^~~~~~~~~~~~~~~~~~~~  
  
      |                                 item_version_type  
  
/celibs/boost_1_74_0/boost/serialization/set.hpp:47:30: error: 'library_version_type' is not a member of 'boost::serialization'; did you mean 'item_version_type'?  
  
   47 |     if(boost::serialization::library_version_type(3) < library_version){  
  
      |                              ^~~~~~~~~~~~~~~~~~~~  
  
      |                              item_version_type  
  
/celibs/boost_1_74_0/boost/serialization/set.hpp:47:56: error: 'library_version' was not declared in this scope  
  
   47 |     if(boost::serialization::library_version_type(3) < library_version){  
  
      |                                                        ^~~~~~~~~~~~~~~  
  
ASM generation compiler returned: 1  
  
In file included from <source>:1:  
  
/celibs/boost_1_74_0/boost/serialization/set.hpp: In function 'void boost::serialization::load_set_collection(Archive&, Container&)':  
  
/celibs/boost_1_74_0/boost/serialization/set.hpp:40:33: error: 'library_version_type' in namespace 'boost::serialization' does not name a type; did you mean 'item_version_type'?  
  
   40 |     const boost::serialization::library_version_type library_version(  
  
      |                                 ^~~~~~~~~~~~~~~~~~~~  
  
      |                                 item_version_type  
  
/celibs/boost_1_74_0/boost/serialization/set.hpp:47:30: error: 'library_version_type' is not a member of 'boost::serialization'; did you mean 'item_version_type'?  
  
   47 |     if(boost::serialization::library_version_type(3) < library_version){  
  
      |                              ^~~~~~~~~~~~~~~~~~~~  
  
      |                              item_version_type  
  
/celibs/boost_1_74_0/boost/serialization/set.hpp:47:56: error: 'library_version' was not declared in this scope  
  
   47 |     if(boost::serialization::library_version_type(3) < library_version){  
  
      |                                                        ^~~~~~~~~~~~~~~  
  
Execution build compiler returned: 1  
```  
  
This is not a problem pre-1.74.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-09-08 18:02:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-689043921  

OK - I fixed this one the develop branch.  Should migrate to master on next release.  Thanks for catching this!

---

## Comment 2

> Username: fcooper8472  
> Created at: 2020-09-09 10:01:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-689461433  

Great, thanks for fixing!

---

## Comment 3

> Username: jagerman  
> Created at: 2020-09-16 16:28:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-693519275  

I also get this failure from `boost/serialization/list.hpp`, but the fix on master looks like it only applies to `set.hpp`.  (Looking at the include dir, I think `slist.hpp` also needs it added).

---

## Comment 4

> Username: robertramey  
> Created at: 2020-09-16 17:08:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-693540530  

right - will do.  Are there any others?

---

## Comment 5

> Username: fcooper8472  
> Created at: 2020-09-16 17:45:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-693559901  

Just gone through to check which headers when included on their own produce compilation errors (with g++-10):  
  
```  
#include <boost/serialization/boost_unordered_map.hpp>  
#include <boost/serialization/boost_unordered_set.hpp>  
#include <boost/serialization/hash_collections_load_imp.hpp>  
#include <boost/serialization/list.hpp>  
#include <boost/serialization/set.hpp>  
#include <boost/serialization/slist.hpp>  
#include <boost/serialization/unordered_collections_load_imp.hpp>  
#include <boost/serialization/unordered_map.hpp>  
#include <boost/serialization/unordered_set.hpp>  
```  
  
The following headers each produced the following error:  
```  
#include <boost/serialization/hash_collections_save_imp.hpp>  
#include <boost/serialization/hash_map.hpp>  
#include <boost/serialization/hash_set.hpp>  
  
error: 'version' was not declared in this scope  
...  
```  
  
The following header produces the following error:  
```  
#include <boost/serialization/optional.hpp>  
  
error: 'version' is not a class template  
...  
```  
  
Finally, there's a file:  
```  
boost/serialization/collection_size_type copy.hpp  
```  
that looks a bit suspect.

---

## Comment 6

> Username: robertramey  
> Created at: 2020-09-27 06:00:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-699590432  

I believe these are fixed.  BUT I haven't run the tests you've run.  So you should run them again.  
  
I didn't get the comment "boost/serialization/collection_size_type copy.hpp" so there may be something to do in that area.  
  
To summarize, this should be better, but somehow I don't think we're quite done yet.  Ideally I'd like to add a bunch of tests for this so the problem can never happen again.  Using B2 these would be "compile only".  I don't remember how it would be done for CMake.  In any case, it would be quite a number of tests.  I'm not sure I'm up for this.

---

## Comment 7

> Username: fcooper8472  
> Created at: 2020-09-28 11:26:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-699947690  

@robertramey just that there are both:  
  
```  
boost/serialization/collection_size_type.hpp  
boost/serialization/collection_size_type copy.hpp  
```  
  
and the latter is the only header with a " copy" in it, and wondered if it was supposed to be there at all.  The contents of the file are substantially different, but it doesn't seem to be referenced anywhere.  
  
The library seems to compile happily without it (although I see the header files are being found using `file(GLOB ...)` in CMake which [not recommended](https://cmake.org/cmake/help/latest/command/file.html#filesystem).  
  
I'm unable to compile the tests though (see errors below which I don't have time right now to chase down), so I can't be certain whether or not that functionality is being used anywhere.  
  
```  
/usr/bin/ld: libwserialization.so: undefined reference to `boost::archive::detail::utf8_codecvt_facet::utf8_codecvt_facet(unsigned long)'  
```

---

## Comment 8

> Username: robertramey  
> Created at: 2020-09-28 15:32:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-700093177  

On 9/28/20 4:26 AM, Fergus Cooper wrote:  
>  
> @robertramey <https://github.com/robertramey> just that there are both:  
>  
> |boost/serialization/collection_size_type.hpp   
> boost/serialization/collection_size_type copy.hpp |  
>  
> and the latter is the only header with a " copy" in it, and wondered   
> if it was supposed to be there at all. The contents of the file are   
> substantially different, but it doesn't seem to be referenced anywhere.  
>  
Ahh - I see this now.  You're right.  It isn't used.  It should be   
removed.  BUT since it's different than the other one I should do a   
little history checking just in case.  
>  
> The library seems to compile happily without it (although I see the   
> header files are being found using |file(GLOB ...)| in CMake which not   
> recommended   
> <https://cmake.org/cmake/help/latest/command/file.html#filesystem>.  
>  
I'm aware that this is not recommended.  But I plead that this is an   
exceptional case.  The number of headers is very large and handling them   
one by one would be very tedious and error prone.  So I'll leave this as   
it is.  
>  
> I'm unable to compile the tests though (see errors below which I don't   
> have time right now to chase down), so I can't be certain whether or   
> not that functionality is being used anywhere.  
>  
> |/usr/bin/ld: libwserialization.so: undefined reference to   
> `boost::archive::detail::utf8_codecvt_facet::utf8_codecvt_facet(unsigned   
> long)'|  
|this has often been a pain point.  I'm not sure what to suggest.  I   
don't have a magic bullet answer.|  
> ||  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/serialization/issues/219#issuecomment-699947690>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAPBDXHGH4O4H5ZRXAPLVJLSIBXGJANCNFSM4RAHRJXQ>.  
>  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 9

> Username: MatheusJulidori  
> Created at: 2022-05-27 17:30:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1139834347  

Seems to be happening again after ubuntu 22.04 update  
  
Tried to make a file and this error was launched:  
  
```  
/usr/include/boost/serialization/unordered_collections_load_imp.hpp:51:27: error: ‘library_version_type’ is not a member of ‘boost::serialization’; did you mean ‘item_version_type’?  
   51 |     boost::serialization::library_version_type library_version(  
      |                           ^~~~~~~~  
      |                           item_version_type  
/usr/include/boost/serialization/unordered_collections_load_imp.hpp:57:30: error: ‘library_version_type’ is not a member of ‘boost::serialization’; did you mean ‘item_version_type’?  
   57 |     if(boost::serialization::library_version_type(3) < library_version){  
      |                              ^~~~~~~~  
      |                              item_version_type  
/usr/include/boost/serialization/unordered_collections_load_imp.hpp:57:56: error: ‘library_version’ was not declared in this scope; did you mean ‘SSLeay_version’?  
   57 |     if(boost::serialization::library_version_type(3) < library_version){  
```

---

## Comment 10

> Username: chrischarlesharrison  
> Created at: 2022-07-12 18:15:44 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1182176264  

I can confirm Matheus' issue with a fresh install of Ubuntu 22.

---

## Comment 11

> Username: pavel-odintsov  
> Created at: 2023-03-07 12:54:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1458122761  

I've used this bugfix https://github.com/jngrad/espresso/commit/f33faf09ab26131bd38c2b8bd33efd35679134a3 for our project and it worked just fine https://github.com/pavel-odintsov/fastnetmon/commit/c0d04543debf001c72dfb097cddb6bf780a42df0

---

## Comment 12

> Username: Jonas-Wessner  
> Created at: 2023-09-03 03:08:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1703994664  

I still get the following error with boost 1.74 which I have just freshly installed with apt-get a couple of minutes ago on Ubuntu 22.04.  
```  
/usr/include/boost/serialization/set.hpp:40:33: error: ‘library_version_type’ in namespace ‘boost::serialization’ does not name a type; did you mean ‘item_version_type’?  
   40 |     const boost::serialization::library_version_type library_version(  
      |                                 ^~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 13

> Username: robertramey  
> Created at: 2023-09-03 19:24:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1704381644  

I've totally lost track of this.  Current version looks OK to see.  So I'm going to close this issue.  Fee free to re-open (again) or make a new issue(better)

---

## Comment 14

> Username: fallaha  
> Created at: 2023-09-06 14:42:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1708509073  

I can confirm this issue (library_version_type) with a fresh install of Ubuntu 22.

---

## Comment 15

> Username: robertramey  
> Created at: 2023-09-06 16:58:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1708769832  

Thinking about this.   Let's assume for now that this issue is only present in Boost 1.74.  And suppose we found it and had a fix.  Then what would we do?  The release date is August 2020.  Would be go back an update the Boost 1.74 package?  Somehow I doubt it.  My suggestion would be to:  
  
- verify if the same issue occurs in the current release.  
- If not - do nothing  
- if it does - fix it in the current release  
- Recommend to users who experience this issue to upgrade the most recent release

---

## Comment 16

> Username: fallaha  
> Created at: 2023-09-06 17:54:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1708841971  

OK, but how can I update it in UBUNTU 22.04 apt package manager?  
can't you update it in UBUNTU repository?

---

## Comment 17

> Username: robertramey  
> Created at: 2023-09-06 17:56:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1708844867  

Sorry, I don't know all the vagaries of boost distribution.

---

## Comment 18

> Username: Jonas-Wessner  
> Created at: 2023-09-07 07:29:34 UTC  
> Updated at: 2023-09-07 07:30:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1709621037  

The issue is that the problem exists in the currently by apt delivered version. (1.74).  
Building and installing from source is a nightmare if you are on a laptop since it will take many hours.  
Anyways, I just made this comment because I think a bug report is in general valuable information. If you don't want to use it, then don't. I also understand that fixing a bug in an old software version is not prioritized.

---

## Comment 19

> Username: angelinesalberto  
> Created at: 2023-09-29 11:06:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/219#issuecomment-1740716116  

I have the same problem with boost-1.82.0 and rocky  
  
/mnt/lustre/home/spack/spack/opt/spack/linux-rocky8-icelake/gcc-8.5.0/boost-1.82.0-v2eykt7r24sm77j3d3lcr7r6qfe4pe3m//include/boost/property_tree/ptree_serialization.hpp:66:24: error: 'library_version_type' in namespace 'bsa' does not name a type  
             const bsa::library_version_type library_version(  
                        ^~~~~~~~~~~~~~~~~~~~  
/mnt/lustre/home/spack/spack/opt/spack/linux-rocky8-icelake/gcc-8.5.0/boost-1.82.0-v2eykt7r24sm77j3d3lcr7r6qfe4pe3m//include/boost/property_tree/ptree_serialization.hpp:69:21: error: 'library_version_type' is not a member of 'bsa'  
             if(bsa::library_version_type(3) < library_version){  
                     ^~~~~~~~~~~~~~~~~~~~  
/mnt/lustre/home/spack/spack/opt/spack/linux-rocky8-icelake/gcc-8.5.0/boost-1.82.0-v2eykt7r24sm77j3d3lcr7r6qfe4pe3m//include/boost/property_tree/ptree_serialization.hpp:69:21: note: suggested alternative:  
In file included from /mnt/lustre/home/spack/spack/opt/spack/linux-rocky8-icelake/gcc-8.5.0/boost-1.82.0-v2eykt7r24sm77j3d3lcr7r6qfe4pe3m//include/boost/serialization/vector.hpp:28,  
                 from ../../../../src/util/serialization.h:39,  
                 from ../../../../src/util/input/input.h:31,  
                 from ../../../../src/util/input/input.cc:27:  
/mnt/lustre/home/spack/spack/opt/spack/linux-rocky8-icelake/gcc-8.5.0/boost-1.82.0-v2eykt7r24sm77j3d3lcr7r6qfe4pe3m//include/boost/serialization/library_version_type.hpp:36:7: note:   'boost::serialization::library_version_type'  
 class library_version_type {  
       ^~~~~~~~~~~~~~~~~~~~  
In file included from ../../../../src/util/serialization.h:46,  
                 from ../../../../src/util/input/input.h:31,  
                 from ../../../../src/util/input/input.cc:27:  
/mnt/lustre/home/spack/spack/opt/spack/linux-rocky8-icelake/gcc-8.5.0/boost-1.82.0-v2eykt7r24sm77j3d3lcr7r6qfe4pe3m//include/boost/property_tree/ptree_serialization.hpp:69:47: error: 'library_version' was not declared in this scope  
             if(bsa::library_version_type(3) < library_version){  
                                               ^~~~~~~~~~~~~~~  
/mnt/lustre/home/spack/spack/opt/spack/linux-rocky8-icelake/gcc-8.5.0/boost-1.82.0-v2eykt7r24sm77j3d3lcr7r6qfe4pe3m//include/boost/property_tree/ptree_serialization.hpp:69:47: note: suggested alternative: 'item_version'  
             if(bsa::library_version_type(3) < library_version){  
                                               ^~~~~~~~~~~~~~~  
                                               item_version
