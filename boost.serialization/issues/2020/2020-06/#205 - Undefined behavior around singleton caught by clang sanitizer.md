# #205 - Undefined behavior around singleton caught by clang sanitizer [Closed]

> Username: correaa  
> Created at: 2020-06-08 18:27:34 UTC  
> Updated at: 2020-07-16 00:05:56 UTC  
> Closed at: 2020-07-15 23:12:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/205  

I know that there was a discussion about singleton in Boost.Serialization.   
I think around linking and stuff like that.   
  
In this case I see that the singleton produces UB as it is caught by clang's undefined-behavior sanitizer:  
  
This simple program  
  
```c++  
#include<boost/archive/text_oarchive.hpp>  
#include<boost/serialization/vector.hpp>  
  
#include<iostream>  
#include<vector>  
  
int main(){  
	std::vector<double> v(10, 99);  
	boost::archive::text_oarchive toa{std::cout};  
	toa << v;  
}  
```  
compiled with `clang++ -fsanitize=undefined test.cpp -o test.x -lboost_serialization` and run produces the following runtime error:  
  
```  
/usr/include/boost/serialization/singleton.hpp:181:13: runtime error: reference binding to null pointer of type 'const boost::serialization::extended_type_info_typeid<std::vector<double, std::allocator<double> > >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/serialization/singleton.hpp:181:13 in   
/usr/include/boost/serialization/singleton.hpp:181:13: runtime error: reference binding to null pointer of type 'const boost::archive::detail::oserializer<boost::archive::text_oarchive, std::vector<double, std::allocator<double> > >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/serialization/singleton.hpp:181:13 in   
```  
  
I don't know if this is a false positive, specially because it referes to a singleton and perhaps it is designed to leak any way. (since `clang` is so popular, perhaps it is worth to whitelist this UB with some clang pragma).  
  
I don't have an opinion on the singleton pattern except  to wonder why would a singleton be necessary in this library.  
  
(Also if one adds `-fsanitize=memory` other problems are caught but I will one a differn issue for that.)

---

## Comment 1

> Username: robertramey  
> Created at: 2020-06-08 18:52:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-640808766  

it's necessary because there is a table of serialization traits (level, etc.) for each type.  And these traits have to be the same if the type is serialized in different places.  A design decision.  Perhaps an ill chosen one but it seemed obvious at the time.  It's the kind of thing that couldn't really be re-visited until a major breaking changes would be introduced.  
  
I would think it might be possible to fix the error message which doesn't seem related to "singletonness" but more to "nullness" applied to a reference.  Would require some careful thought - be my guest.  
  
Robert Ramey

---

## Comment 2

> Username: correaa  
> Created at: 2020-06-08 19:27:08 UTC  
> Updated at: 2020-06-08 19:37:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-640831115  

Yes, I agree with the diagnosis.   
  
It seems that this registration of types was a common design pattern at the time.   
I am also dealing with MPI (message passing library over network), which under the covers needs something like type registration, and it has a similar problem.   
That is that types need to be registered (commited) to be used later.   
http://mpi.deino.net/mpi_functions/MPI_Type_commit.html  
  
The problem is that in the common usage and examples (that is ugly C-code) they basically show that you  have to register the types at the beginning of the program and it is in effect difficult to manage locally because one is dealing with something that (from the API) looks like a singleton.

---

## Comment 3

> Username: robertramey  
> Created at: 2020-06-08 19:51:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-640851255  

We register serialization traites on first use of the type - much more practical.  BTW I don't know that it's even possible to eliminate these singletons.  But I do know that I needed a solution to a difficult problem and that worked.  Today I might have spent more time on the problem.  But using the theory, the library would never have been finished.  Some times the perfect is the enemy of the good.

---

## Comment 4

> Username: correaa  
> Created at: 2020-06-08 23:03:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-640931753  

Well, yes, I wouldn't know if there is an alternative even now.   
Registration on first use seems a good choice and it is practical but if there is _any_ runtime information (as in MPI) (e.g. strides, etc) effectively once could register too many "types" and never free them.  
  
After all, information about types is essentially global and a hack for something that really needs some kind of reflection in the language.   
Perhaps these days instantiation of `constexpr` structures could replace the singleton for Boost.Serialization application in particular.  
  
Perhaps an alternative to singleton would be to attach that information to the archives individually, either internally or passes when constructing the archives.  
But I am of course underestimating the challenges since I don't fully understand the scope of Boost.Seriaization like pointer tracking and polymorphic serialization.   
There are features that I don't use or try to use.

---

## Comment 5

> Username: robertramey  
> Created at: 2020-06-08 23:32:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-640941125  

"Perhaps an alternative to singleton would be to attach that information to the archives individually, either internally or passes when constructing the archives"  Actually this is already done since a data item needs to be read the same way it was written - even if the serialization trait was changed in the mean time.  A big challenge is that serialization defined within DLLS needs to keep it's own table - same as normal variables.  So it's important that one define All of serialization of a specific type be defined in one and only one run time module - be it dll or exe.  All in all this is much, much, much more intricate than almost anyone recognizes.  I doubt there will ever be another serialization library which considers all of this - standard or otherwise.  There's a place for a simpler but more limited one - such as cereal.  But users always pipe how they need feature x and it's not that hard because project X does it.  Believe me, no other library supports everything in combination like Boost Serialization does.  As far as I know, no one has even made a proposal for standards based serialization - much less promoted it.  
  
As I've said, I do have reservations about the method I chose - but no one as noticed them or complained about them and I'm tired of working on it and no one has offered to pay me.  Some people do complain about this or that though.

---

## Comment 6

> Username: robertramey  
> Created at: 2020-06-08 23:33:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-640941342  

FYI - you're likely using pointer tracking without even knowing it.

---

## Comment 7

> Username: correaa  
> Created at: 2020-06-11 08:56:48 UTC  
> Updated at: 2020-06-11 09:03:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-642510426  

My bad. I should have followed the link and read the comments in your code:  
  
```c++  
        // note that the following is absolutely essential.  
        // commenting out this statement will cause compilers to fail to  
        // construct the instance at pre-execution time.  This would prevent  
        // our usage/implementation of "locking" and introduce uncertainty into  
        // the sequence of object initialization.  
        // Unfortunately, this triggers detectors of undefine behavior  
        // and reports an error.  But I've been unable to find a different  
        // of guarenteeing that the the singleton is created at pre-main time.  
         use(* m_instance);  
```  
  
What I did first was of course to comment this line and the UB warning went away.   
I tried with my compilers, clang-10, gcc--9, nvcc-10, icc-14 and the application worked.  
It could be that modern compilers do not need this workaround in the first place or that my use case  is too simple to catch this error. I have only a vague idea of why this might be needed, if it for linkage/dll stuff (as you usually mention on your answers) I would be completely lost.  
  
In any case, I could suggest something that may make it work with out generating UB: replace the line by:  
  
```c++  
         if(m_instance) use(*m_instance);  
```  
  
Sorry if underestimate the work that lead you to settle with this code.  
I feel I will never be able to grasp the scope and corner cases contemplated by Serialization.

---

## Comment 8

> Username: robertramey  
> Created at: 2020-07-15 23:09:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-659060693  

>I feel I will never be able to grasp the scope and corner cases contemplated by Serialization.  
  
That makes two of us

---

## Comment 9

> Username: robertramey  
> Created at: 2020-07-15 23:12:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-659061513  

> if(m_instance) use(*m_instance);  
  
I've found this in the current code.  So I guess I put it in.  So I'm going to close this.

---

## Comment 10

> Username: correaa  
> Created at: 2020-07-16 00:05:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/205#issuecomment-659077934  

ok, I was looking at an older version in my local distribution. Glad this  
was fixed before (and in the same way).  
  
On Wed, Jul 15, 2020 at 4:12 PM Robert Ramey <notifications@github.com>  
wrote:  
  
> if(m_instance) use(*m_instance);  
>  
> I've found this in the current code. So I guess I put it in. So I'm going  
> to close this.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/205#issuecomment-659061513>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAXICU2CV5CUKN527D7FHZ3R3YZV3ANCNFSM4NYVASVQ>  
> .  
>
