# #71 - Consider making the library header only [Closed]

> Username: alfC  
> Created at: 2017-11-02 03:37:40 UTC  
> Updated at: 2022-08-16 21:45:01 UTC  
> Closed at: 2022-08-16 21:44:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/71  

The library always need to be linked against `-lboost_serialization`.   
It would be great to make the library header only, or optionally header only (like Boost.ASIO).  
  
For certain uses (e.g. not using default archives) I made it work as header only, by #including some .cpp from the source. Which shows that with some changes it would be possible to make it header only, and in particular if one wants to use the serialization infrastructure.  
  
What are the obstacles to make the library header only?  
  
---------  
note: https://stackoverflow.com/questions/44404752/is-it-possible-to-use-boost-serialization-as-a-header-only-library

---

## Comment 1

> Username: robertramey  
> Created at: 2017-11-02 03:49:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-341310418  

At the time the library was written, Code Bloat as of much more concern than it is today.  It seems that no one raises the issue today.  That why it is the way it is.  It could also make the library simpler as it now transfers back and forth between void * and T* in order to share precompiled code in the library. And it could double the speed of the library.  Cereal is about twice as fast as BS. Of course the minute that you raise the possibility of making this change, a howl will go up - as it does whenever you change something widely used.  This would optimally addressed by making it an option - but that might windup to make the library so complex as to defeat the purpose.  Implementation is already quite complex.  So it would seem to me be quite an ambitious project - certainly one that I would not be willing to undertake.  
  
I'm impressed with your success so far.  You likely know a lot more than I do at this point about what this would take.

---

## Comment 2

> Username: alfC  
> Created at: 2017-11-02 07:24:32 UTC  
> Updated at: 2017-11-02 09:58:58 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-341338065  

Well, I certainly understand that the library was made with the mindset for another era.   
Actually, it tells something good about the design that the utility persisted.  
  
There are several aspects to the problem.   
I would say that the library is two libraries in one, one that defines the protocol of serialization (amazing that without using sfinae the library is quite usable) and another that handles the archives.  
  
Sometimes I use the library just to exploit the protocol.   
That is, a BS-compatible class is used for something else, perhaps for a custom made archive.  
This usage still needs to link to `boost_serialization`, but I don't see a fundamental reason for it.   
As a matter of fact if I inelegantly include this bunch of cpp files, I create the illusion of a header-only library for this purposes.  
  
```  
// use this to avoid need for linking  
#ifdef MAKE_BOOST_SERIALIZATION_HEADER_ONLY  
#include "../../serialization_hack/archive_exception.cpp"  
#include "../../serialization_hack/extended_type_info.cpp"  
#include "../../serialization_hack/extended_type_info_typeid.cpp"  
#include "../../serialization_hack/basic_archive.cpp"  
#include "../../serialization_hack/basic_iarchive.cpp"  
#include "../../serialization_hack/basic_oarchive.cpp"  
#include "../../serialization_hack/basic_iserializer.cpp"  
#include "../../serialization_hack/basic_oserializer.cpp"  
#endif  
```  
So, that's it, if my custom archive is header only, and I only want to use the serialization, I can get away with this. (this is a hack because I have to make sure that the version of the headers and the cpps are compatible).  
  
Making the archive (binary/xml/etc) utility part header-only seems to be more problematic.   
Because most functions are not templates and the natural way is to make it not header-only.  
What I found lately is that your can force some libraries it to be be header-only by making functions `inline`.  
Now, you may or may not want to do this. However, I am convinced that if the functions defined in the few `cpp` files above are moved to the hpps then a user of the serialization protocol alone will be able to use it as a header-only.  
  
Ok, this is as much as I know how to make your library header only.   
There are things that I didn't study, like versioning, and tracking of pointers.  
These are thing I don't use, mainly because I think it should be responsibility of the implementation of the protocol (user save/load functions) to serialize (shared/unique)pointers (and not of the serialization library).  
  
So, in summary, perhaps I can change the title of this issue, "Consider making the protocol part of the library header-only".

---

## Comment 3

> Username: robertramey  
> Created at: 2017-11-08 17:21:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-342890251  

"Consider making the protocol part of the library header-only"  
  
I'm not going to do this.  It's way too much work and not worth it.  
  
If the usage as a library is problem for you, just import the *.cpp files directly into your project.  I'm pretty sure this would work fine.

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-10-17 15:26:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-430674157  

Too bad this was closed. I am seconding this request, there are many good ideas here, in particular the notion that the serialization protocol could be header-only and split from the implementation of archives.

---

## Comment 5

> Username: robertramey  
> Created at: 2018-10-17 15:42:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-430680650  

LOL - there's room for and maybe demand for serialization2.  Of maybe even std::serialization!

---

## Comment 6

> Username: robertramey  
> Created at: 2019-02-24 19:21:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-466807625  

>Too bad this was closed. I am seconding this request, there are many good ideas here, in particular the notion that the serialization protocol could be header-only and split from the implementation of archives.  
  
There's no reason that you guys couldn't do it.    
  
I did take a look at implementing a better "split" between serialization and archive.  This was in response to complaint that the serialization library (being a "bridge") library made library dependencies circular.  
  
I concluded that it would be quite doable as serialization as serialization has dependency on archive in only a couple of places.  I designed the split with this in mind.  But as time went on, I backslide and a few crept in.  If someone want's to implement that it would be interesting to see if one could do that without re-inventing the whole thing.

---

## Comment 7

> Username: alfC  
> Created at: 2019-02-24 20:05:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-466811679  

So, I managed to do the splitting by including a certain set of heders and  
defining macros. I will post an update of that code here.  
  
But I am not sure if it works in general. Do you think it is not possible?,  
for example by adopting my hack, without changing other core parts of the  
library?  
  
On Sun, Feb 24, 2019, 11:21 Robert Ramey <notifications@github.com> wrote:  
  
> Too bad this was closed. I am seconding this request, there are many good  
> ideas here, in particular the notion that the serialization protocol could  
> be header-only and split from the implementation of archives.  
>  
> There's no reason that you guys couldn't do it.  
>  
> I did take a look at implementing a better "split" between serialization  
> and archive. This was in response to complaint that the serialization  
> library (being a "bridge") library made library dependencies circular.  
>  
> I concluded that it would be quite doable as serialization as  
> serialization has dependency on archive in only a couple of places. I  
> designed the split with this in mind. But as time went on, I backslide and  
> a few crept in. If someone want's to implement that it would be interesting  
> to see if one could do that without re-inventing the whole thing.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/71#issuecomment-466807625>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACMfYL4FFUcH9zMZ1J2LlZccDkXeUIiHks5vQuY5gaJpZM4QPIGN>  
> .  
>

---

## Comment 8

> Username: correaa  
> Created at: 2019-02-25 03:02:01 UTC  
> Updated at: 2019-02-25 03:03:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-466855804  

This is the current version of my hack. What I would like to know, is if you think this is robust. If it is robust then that means that there is a chance that this can be used to separate the library in two, simply by moving some cpp code into hpp code.  
  
In my header file I do this:  
```  
// use this to avoid need for linking -lserialization  
#ifdef _MAKE_BOOST_SERIALIZATION_HEADER_ONLY  
//#include <boost/archive/detail/decl.hpp>  
#if BOOST_VERSION > 106000 && BOOST_VERSION < 106600  
#include "../mpi3/serialization_hack/singleton.cpp"  
#endif  
#if BOOST_VERSION < 105900  
#define BOOST_ARCHIVE_DECL  
#define BOOST_SERIALIZATION_DECL  
#endif  
#include "../mpi3/serialization_hack/archive_exception.cpp"  
#include "../mpi3/serialization_hack/extended_type_info.cpp"  
#include "../mpi3/serialization_hack/extended_type_info_typeid.cpp"  
#include "../mpi3/serialization_hack/basic_archive.cpp"  
#include "../mpi3/serialization_hack/basic_iarchive.cpp"  
#include "../mpi3/serialization_hack/basic_oarchive.cpp"  
#include "../mpi3/serialization_hack/basic_iserializer.cpp"  
#include "../mpi3/serialization_hack/basic_oserializer.cpp"  
#endif  
```  
(see https://gitlab.com/correaa/boost-mpi3/blob/master/communicator.hpp)  
  
Where the cpp files are stolen from the Boost *cpp sources*. For example there is a snapshot of these sources in https://gitlab.com/correaa/boost-mpi3/tree/master/serialization_hack

---

## Comment 9

> Username: robertramey  
> Created at: 2019-03-01 23:22:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-468845701  

there are two things going on here:  
a) desire for other libraries to be able to include support for serialization without sucking in a boatload of other files.  This is the dependency complaint.  To address it would require that the library be split into two libraries  - serialization and archive.  In theory this wouldn't be too bad.  In practice I fear it would create unanticipated issues - and I'm not sure that it would really address the concerns raised by the those concerned by dependencies.  
b) desire to include serialization as header only by creating a module in your project which includes *.cpp files is a time honored technique for elimination the pre-compiled library hassle.  I've used it myself on multiple occasions and it's worked great.   But I notice that it's not widely used. Note I would suggest a variation on this technique: create a static library as part of your project which includes the *.cpp files you want to use.  Build the "custom" static lib with the switches which make each function a module (forget the name).  Then when you build the real project you get only the code which is actually called.  Since it's all the same project, you don't have any hassle with API, or trying to get all the build switches in sync.   I think it would be a great idea if you wanted to write some sort of tutorial about this so it would be more widely used.

---

## Comment 10

> Username: correaa  
> Created at: 2019-03-02 23:18:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-468969810  

On Fri, Mar 1, 2019 at 3:22 PM Robert Ramey <notifications@github.com>  
wrote:  
  
> there are two things going on here:  
> a) desire for other libraries to be able to include support for  
> serialization without sucking in a boatload of other files. This is the  
> dependency complaint. To address it would require that the library be split  
> into two libraries - serialization and archive. In theory this wouldn't be  
> too bad. In practice I fear it would create unanticipated issues - and I'm  
> not sure that it would really address the concerns raised by the those  
> concerned by dependencies.  
> b) desire to include serialization as header only by creating a module in  
> your project which includes *.cpp files is a time honored technique for  
> elimination the pre-compiled library hassle. I've used it myself on  
> multiple occasions and it's worked great. But I notice that it's not widely  
> used. Note I would suggest a variation on this technique: create a static  
> library as part of your project which includes the *.cpp files you want to  
> use. Build the "custom" static lib with the switches which make each  
> function a module (forget the name). Then when you build the real project  
> you get only the code which is actually called. Since it's all the same  
> project, you don't have any hassle with API, or trying to get all the build  
> switches in sync. I think it would be a great idea if you wanted to write  
> some sort of tutorial about this so it would be more widely used.  
>  
I don't have experience building and linking static libraries, also I did  
the hack of moving cpp code to hpp to simplify compilation only a couple of  
times. (Precisely because I don't want to deal with linking).  
  
I can explain how I did it for boost serialization, but I did it in a very  
ad hoc way, trial and error.  
  
 I don't know if I will be giving bad advise at the end.

---

## Comment 11

> Username: henryiii  
> Created at: 2019-09-17 14:13:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-532239841  

This would be *very* useful for the Python bindings for Boost::Histogram. Currently, all Boost dependencies are header only, so the library builds with `setup.py` or CMake, and no dependencies other than git submodules (which get bundled into the source tarball when making a Python SDist). The one sticking point is serialization; the library has to use an alternate serialization method because this library doesn't have a header-only mode. (However, I have not yet tried the CPP include method mentioned above, which seems a little hacky and expects the .cpp file names not to change, but possibly doable).

---

## Comment 12

> Username: robertramey  
> Created at: 2019-09-20 19:12:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-533676840  

> I don't have experience building and linking static libraries, also I did  
the hack of moving cpp code to hpp to simplify compilation only a couple of  
times. (Precisely because I don't want to deal with linking).  
  
You don't have to move any cpp files to cpp.  Just include the cpp files in your project.

---

## Comment 13

> Username: correaa  
> Created at: 2019-09-20 19:27:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-533680953  

> > I don't have experience building and linking static libraries, also I did  
> > the hack of moving cpp code to hpp to simplify compilation only a couple of  
> > times. (Precisely because I don't want to deal with linking).  
>   
> You don't have to move any cpp files to cpp. Just include the cpp files in your project.  
  
That would be ideal, but in most linux distributions the cpp files are not available. So for example I have to choose a particular version of the library and copy the cpp files into the project.  
I don't know if there is an easy way to get packages with sources (I am talk about Debian and Fedora here).

---

## Comment 14

> Username: robertramey  
> Created at: 2019-09-20 19:45:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-533686099  

just got to GitHub and  clone or download the boost/serializaton library.  Or you might have to clone/download and install all of boost source.

---

## Comment 15

> Username: correaa  
> Created at: 2019-09-20 19:54:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-533688791  

> just got to GitHub and clone or download the boost/serializaton library. Or you might have to clone/download and install all of boost source.  
  
yes, that's exactly what I mean. 1) choose a version of serialization sources from github (hopefully compatible with the automatically maintained distribution) 2) downloading/copying it to somewhere in your disk. 3) include the cpp's in the appropriate order (unfortunately this needs some minor manual changes in the cpp to be compatible with different version of the hpp existing in your linux distributions).   
  
Yes, moving text code from cpp to hpp is not strictly necessary, but it is not the  worst part given the other steps necessary.

---

## Comment 16

> Username: robertramey  
> Created at: 2022-08-16 21:45:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/71#issuecomment-1217199917  

no movement in a while so I'm closing this.  Git hub could use a flag open/closed/inactive rather than just open/close
