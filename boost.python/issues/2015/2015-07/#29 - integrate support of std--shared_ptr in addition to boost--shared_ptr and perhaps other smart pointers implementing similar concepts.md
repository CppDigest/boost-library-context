# #29 - integrate support of std::shared_ptr in addition to boost::shared_ptr and perhaps other smart pointers implementing similar concepts [Closed]

> Username: nevion  
> Created at: 2015-07-27 05:13:24 UTC  
> Updated at: 2016-11-09 15:34:27 UTC  
> Closed at: 2016-09-29 11:44:53 UTC  
> Url: https://github.com/boostorg/python/issues/29  

currently the support of boost::shared_ptr islands code using std::shared_ptr.  While in some cases the user can simply choose to use boost::shared_ptr over std::shared_ptr, this does not work on a pre-existing library whom chose to use std::shared_ptr.  
  
It may also be worth considering older libraries out there who'm had chosen to implement their own smart ptr ( e.g. OpenSceneGraph, ITK/VTK) though I'm not sure they'll support 1:1 the same exact concepts shared_ptr does.

---

## Comment 1

> Username: owenhaynes  
> Created at: 2015-09-01 09:54:27 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-136657601  

Could do with this.

---

## Comment 2

> Username: bhack  
> Created at: 2015-09-19 10:31:43 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-141643634  

:+1:

---

## Comment 3

> Username: eudoxos  
> Created at: 2015-09-27 09:41:35 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-143533354  

This is the original ticket: https://svn.boost.org/trac/boost/ticket/6545 and the missing functionality with std::shared_ptr (no implciit upcasting) was discussed at c++-sig http://thread.gmane.org/gmane.comp.python.c++/15759/focus=15771 .

---

## Comment 4

> Username: KayEss  
> Created at: 2015-12-19 04:02:44 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-165944589  

Did something change here between 1.59 and 1.60? I'm just testing the new version and we're getting testing errors now with things like:  
  
```  
TypeError: No to_python (by-value) converter found for C++ type: std::shared_ptr<fostlib::jsondb::local>  
```  
  
We have recently changed these from using boost::shared_ptr to std::shared_ptr, but everything works in Boost 1.56 to 1.59 but it fails now with 1.60. The wrapper is [here](https://github.com/KayEss/fost-py/blob/master/Cpp/fost-json-py/json-py.cpp#L40).  
  
I saw the post linked above, but have no idea where to put that. I don't think this is affected anyway as there is no class hierarchy involved.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2015-12-19 04:11:13 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-165945606  

I'm not aware of any related changes in Boost.Python. In particular, I don't think std::shared_ptr was ever supported in Boost.Python (I do want to add support for this, but haven't had the time to set up builds with a C++11 compatible compiler to test this.)

---

## Comment 6

> Username: KayEss  
> Created at: 2015-12-19 04:17:06 UTC  
> Updated at: 2015-12-19 05:35:37 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-165945780  

Thanks. Sounds like I ought to convert all of the std::shared_ptr to boost::shared_ptr though anyway. Hopefully that will work.  
  
EDIT: Ok, that didn't work either. I don't really have time to dive down deeply into this today. Will try again in a few days

---

## Comment 7

> Username: ycollet  
> Created at: 2016-01-07 10:18:04 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-169619903  

The same for me here.  
Our application works fine with boost-1.59 and fails with boost-1.60 with problems related to shared_ptr.

---

## Comment 8

> Username: KayEss  
> Created at: 2016-01-08 05:10:52 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-169898186  

@ycollet What are the actual errors that you see? Are you seeing the same sort of errors we are? We're getting problems in the type registry:  
  
```  
TypeError: No to_python (by-value) converter found for C++ type: boost::shared_ptr<fostlib::jsondb::local>  
```  
  
I've switched between `std::shared_ptr` and `boost::shared_ptr` and it doesn't make any difference. I wonder what changes were made to `shared_ptr` between 1.59 and 1.60....

---

## Comment 9

> Username: ycollet  
> Created at: 2016-01-08 07:24:55 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-169917082  

I've got the same error message as you:  
  
TypeError: No to_python (by-value) converter found for C++ type: boost::shared_ptr<*>  
  
----- Mail original -----  
De: "Kirit Sælensminde" notifications@github.com  
À: "boostorg/python" python@noreply.github.com  
Cc: "ycollet" ycollette.nospam@free.fr  
Envoyé: Vendredi 8 Janvier 2016 06:10:53  
Objet: Re: [python] integrate support of std::shared_ptr in addition to boost::shared_ptr and perhaps other smart pointers implementing similar concepts (#29)  
  
@ycollet What are the actual errors that you see? Are you seeing the same sort of errors we are? We're getting problems in the type registry: TypeError: No to_python (by-value) converter found for C++ type: boost::shared_ptrfostlib::jsondb::local   
  
I've switched between std::shared_ptr and boost::shared_ptr and it doesn't make any difference. I wonder what changes were made to shared_ptr between 1.59 and 1.60....   
  
—   
Reply to this email directly or view it on GitHub .

---

## Comment 10

> Username: KayEss  
> Created at: 2016-01-08 07:34:51 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-169918144  

There's nothing in the release notes about changes to either the python or pointer libraries. I think it's fair to say that if we're both getting the same sort of problem it's less likely that we're both doing the same stupid thing that triggers it.  
  
I've been meaning to set up a build mechanism that would allow me to build against the current development version of Boost. If I can do that then bisecting between 1.59 and 1.60 might find the problem.  
  
BTW, I'm doing C++14 builds and get the same problem on both gcc and clang.

---

## Comment 11

> Username: ycollet  
> Created at: 2016-01-08 08:17:19 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-169928933  

I fear that this problem comes from a change from a change on another lib.   
I had a look at the changes between 1.59 and 1.60 and nothing really evident shows up.

---

## Comment 12

> Username: stefanseefeld  
> Created at: 2016-01-08 12:39:24 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-169987662  

Thanks for investigating this !   
Just FYI, I'm experimenting with ways to build Boost.Python stand-alone (using something other than Boost.Build), to make it easier to maintain and perhaps even develop. Right now I'm still stuck with Boost.Build, and I have a hard time doing anything with it, including trying to change compiler flags (such as adding --std=c++11) so I can experiment with adding support for std::shared_ptr.

---

## Comment 13

> Username: KayEss  
> Created at: 2016-01-11 04:22:08 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-170427723  

@stefanseefeld This may help you: https://github.com/KayEss/fost-py/blob/master/Cpp/fost-json-py/Jamfile  
  
The `$TARGET_REQUIREMENTS` are defined in https://github.com/KayEss/fost-base/blob/master/fost.jam#L37 You'll notice it includes C++14 flags.  
  
It is also possible to pass in compiler flags to the bjam command line, but I forget how to do that :) It is nearly impossible to find this stuff in the docs :(

---

## Comment 14

> Username: finjulhich  
> Created at: 2016-01-11 12:05:51 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-170521239  

you can ask on the boost or boost-build mailing list, from experience,  
Vladimir answers promptly.  
  
On 11 January 2016 at 04:22, Kirit Sælensminde notifications@github.com  
wrote:  
  
> @stefanseefeld https://github.com/stefanseefeld This may help you:  
> https://github.com/KayEss/fost-py/blob/master/Cpp/fost-json-py/Jamfile  
>   
> The $TARGET_REQUIREMENTS are defined in  
> https://github.com/KayEss/fost-base/blob/master/fost.jam#L37 You'll  
> notice it includes C++14 flags.  
>   
> It is also possible to pass in compiler flags to the bjam command line,  
> but I forget how to do that :) It is nearly impossible to find this stuff  
> in the docs :(  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/python/issues/29#issuecomment-170427723.

---

## Comment 15

> Username: jhunold  
> Created at: 2016-01-11 12:40:33 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-170533815  

See http://www.boost.org/build/doc/html/bbv2/overview/builtins/features.html for the "cxxflags" feature.  
Or just grep the Jamfiles in the Boost sources for "-std=" to find existing examples.

---

## Comment 16

> Username: bennybp  
> Created at: 2016-02-04 01:53:41 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-179570851  

I'm also experiencing the error with boost::shared_ptr referenced by @KayEss and @ycollet. I managed to bisect with a simple test program and script. It appears to come from an update/rewrite of type_traits around commit f0da159e1f52e87ad71e4747de6b042c54578e0e (in the type_traits submodule, although that commit won't compile for me).  
  
Unfortunately, it looks like a big rewrite in one commit, so I don't know the exact cause, but I hope this helps

---

## Comment 17

> Username: KayEss  
> Created at: 2016-02-04 03:36:38 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-179601130  

Well found @bennybp [Here's a link to the commit.](https://github.com/boostorg/type_traits/commit/f0da159e1f52e87ad71e4747de6b042c54578e0e) Not really surprised there was a regression in it given how large it is.

---

## Comment 18

> Username: epot  
> Created at: 2016-02-05 16:21:35 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-180427381  

Concerning the boost regression between 1.59 and 1.60, I had a similar one ad I created a post on stackoverflow about it:  
http://stackoverflow.com/questions/35056398/typeerror-when-iterating-over-a-boostpython-vector  
  
If we do have the same issue, it is completely unrelated to smart pointers.

---

## Comment 19

> Username: eudoxos  
> Created at: 2016-02-10 08:58:56 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-182260945  

Just as a reminder, the problem with missing converter which arose between 1.59 and 1.60 is, as far as I see, orthogonal to this issue (though certainly valid) -- which is to support std::shared_ptr (and perhaps other smart pointer types) at the same level as boost::shared_ptr (including e.g. automatic upcasting).

---

## Comment 20

> Username: epot  
> Created at: 2016-02-10 09:00:07 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-182261964  

I definitely agree: I have this issue without any smart pointer.  
  
On Wed, 10 Feb 2016 at 09:58 eudoxos notifications@github.com wrote:  
  
> Just as a reminder, the problem with missing converter which arose between  
> 1.59 and 1.60 is, as far as I see, orthogonal to this issue (though  
> certainly valid) -- which is to support std::shared_ptr (and perhaps other  
> smart pointer types) at the same level as boost::shared_ptr (including e.g.  
> automatic upcasting).  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/python/issues/29#issuecomment-182260945.

---

## Comment 21

> Username: wojdyr  
> Created at: 2016-02-11 13:33:58 UTC  
> Updated at: 2016-02-11 13:34:27 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-182864146  

@eudoxos @epot yes, so I just opened a separate issue for the regression, linked above

---

## Comment 22

> Username: stefanseefeld  
> Created at: 2016-09-27 18:41:13 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-249958049  

I have just merged support for `std::shared_ptr` into the `develop` branch (https://github.com/boostorg/python/commit/97e4b34a15978ca9d7c296da2de89b78bba4e0d5), so whenever Boost.Python is compiled with a C++11-compliant compiler std::shared_ptr is supported in exactly the same way as `boost::shared_ptr`. I plan to add some documentation (in particular release notes), then prepare for this to be merged into master for the next (i.e., 1.63) release.

---

## Comment 23

> Username: NOhs  
> Created at: 2016-11-09 12:17:05 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-259401758  

I can't find this feature in the notes for the 1.63 release [svn-milestone-1.63-link](https://svn.boost.org/trac/boost/query?group=status&milestone=Boost+1.63.0). Is it just not in that list, or will it be delayed?

---

## Comment 24

> Username: stefanseefeld  
> Created at: 2016-11-09 12:48:45 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-259407108  

It will be part of 1.63. See http://www.boost.org/users/history/version_1_63_0.html for tentative release notes. (I doubt trac will reflect current efforts, as more and more projects move over to github issue tracking.)

---

## Comment 25

> Username: NOhs  
> Created at: 2016-11-09 15:34:27 UTC  
> Url: https://github.com/boostorg/python/issues/29#issuecomment-259443344  

Nice. Looking forward to it!
