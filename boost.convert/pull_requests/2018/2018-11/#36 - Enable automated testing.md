# #36 Enable automated testing [Closed]

> Username: CromwellEnage  
> Created at: 2018-11-10 12:53:20 UTC  
> Updated at: 2018-11-12 01:39:36 UTC  
> Closed at: 2018-11-12 01:36:48 UTC  
> Url: https://github.com/boostorg/convert/pull/36  

Also update boost::cnv::cnvbase and boost::cnv::basic_stream to use public interface of Boost.Parameter vice boost::parameter::aux::tag.

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2018-11-11 00:15:01 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437632337  

Would you mind elaborating what your changes improve or what problems do they solve please? Additional functionality? Additional support? Fixes? So far, I see additional dependencies on boost/mpl and boost/parameter which I do not immediately see as beneficial. Thanks.

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2018-11-11 02:45:45 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437638990  

In a nutshell, this PR fixes the errors that showed up on the last PR whose commit you merged.  The errors stem from using boost::parameter::aux::tag, a metafunction that is internal to Boost.Parameter and that may therefore be subject to interface and/or implementation changes.  The code changes featured in this PR use the public interface of the ArgumentPack (which models MPL Associative Sequence) to perform the desired assignment operations if the corresponding arguments are available, which was the original intent.

---

## Comment 3

> Username: yet-another-user  
> Created_at: 2018-11-11 09:18:05 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437654746  

1. I merged two "yml" files. Are they related to the Boost testing framework or they are the artifacts of your testing framework?   
  
2. In the current outstanding pull request I see quite a few changes... some of them style. For ex., "template<typename>" vs. "template <typename>". Was it essential? I acknowledge your differing stylistic preferences. However, that creates more review work for me IMO unnecessarily... and that assumes that I'll be willing to adopt your style... when you decided _not_ to adopt mine. :-)    
  
3. You state that the "errors stem from"... In the Boost framework the existing code was not causing any errors. Has the Boost framework changed?..   
  
4. You state that "boost::parameter::aux::tag... _may_ ... be subject to interface and/or implementation changes". Indeed it may. Is it an _actual_ problem or it is a _potential_ problem? If it is the latter, then I am reluctant introducing voluminous changes without the actual need, sufficient testing and code review. In my view, "do not fix it if it ain't broken".

---

## Comment 4

> Username: CromwellEnage  
> Created_at: 2018-11-11 14:19:53 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437674050  

1. I created the two YML files simply as a courtesy so that Boost.Convert could be tested automatically by Travis Cl.  (I don't know why it's not being tested by AppVeyor.)  They have nothing to do with any custom testing framework by myself, Boost.Parameter, Boost.Test, etc.  
2. I've reverted to your coding style as much as I could understand it.  
3. Boost.Parameter's public interface has not introduced backwards-incompatible changes, only expanded.  Boost.Parameter's internals, like those of any other library, are free to change without notice.  
4. I'll remember not to use the phrase "may be" when I mean "is" or "will be".  Yes, using boost::parameter::aux::tag directly is an actual problem: it allows user libraries such as Boost.Convert to be broken by changes to boost::parameter::aux::tag and/or other components internal to Boost.Parameter.  I've made the same mistake relying on the presence and behavior of a few of Boost.Graph's internals in my own projects, and I haven't complained when those internals changed.

---

## Comment 5

> Username: yet-another-user  
> Created_at: 2018-11-11 21:22:36 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437706167  

Your idea of avoiding the internal boost::parameter API looks appealing... Upgrading from deprecated  BOOST_PARAMETER_KEYWORD to BOOST_PARAMETER_NAME as well... while I am at it. :-) Now, when I try building, I get the following:   
stream.hpp:101:50: error: ‘is_argument_pack’ is not a member of ‘boost::parameter’  
  
and indeed I cannot find "is_argument_pack" anywhere... as I cannot find boost/parameter/is_argument_pack.hpp. Using Boost 1.68. What am I missing?

---

## Comment 6

> Username: CromwellEnage  
> Created_at: 2018-11-11 21:42:57 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437707704  

The metafunction boost::parameter::is_argument_pack is currently only  
available in the development branch of https://github.com/boostorg/parameter  
and, like the rest of the changes there, won’t be released until Boost 1.70  
at the earliest.  But you can always download & unzip into your local  
environment, then make sure your compiler searches its include directory  
before your main Boost distribution.  
  
On Sun, Nov 11, 2018 at 16:22 Mike <notifications@github.com> wrote:  
  
> Your idea of avoiding the internal boost::parameter API looks appealing...  
> Upgrading from deprecated BOOST_PARAMETER_KEYWORD to BOOST_PARAMETER_NAME  
> as well... while I am at it. :-) Now, when I try building, I get the  
> following:  
> stream.hpp:101:50 <https://maps.google.com/?q=101:50&entry=gmail&source=g>:  
> error: ‘is_argument_pack’ is not a member of ‘boost::parameter’  
>  
> and indeed I cannot find "is_argument_pack" anywhere... as I cannot find  
> boost/parameter/is_argument_pack.hpp. Using Boost 1.68. What am I missing?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/pull/36#issuecomment-437706167>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsL2IXKBBpD-_pV3KrMaByzLAgXvvks5uuJUcgaJpZM4YX-QU>  
> .  
>

---

## Comment 7

> Username: yet-another-user  
> Created_at: 2018-11-11 22:04:06 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437709288  

> The metafunction boost::parameter::is_argument_pack is currently only available in the development branch of https://github.com/boostorg/parameter and, like the rest of the changes there, won’t be released until Boost 1.70 at the earliest.  
  
I suspected so. :-( I am not sure if it is a good idea of "convert" relying on "parameter/development". After all, the idea behind all these changes was to replace the internal parameter API (subject to change). We should not be replacing it with just another subject-to-change API. Agree?

---

## Comment 8

> Username: CromwellEnage  
> Created_at: 2018-11-11 23:05:08 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437713915  

The metafunction *will* be part of Boost.Parameter’s public interface, and  
the HTML reference documentation will reflect this.  It just won’t make the  
Boost 1.69 release because that’s already closed to major changes.  
  
On Sun, Nov 11, 2018 at 17:04 Mike <notifications@github.com> wrote:  
  
> The metafunction boost::parameter::is_argument_pack is currently only  
> available in the development branch of  
> https://github.com/boostorg/parameter and, like the rest of the changes  
> there, won’t be released until Boost 1.70 at the earliest.  
>  
> I suspected so. :-( I am not sure if it is a good idea of "convert"  
> relying on "parameter/development". After all, the idea behind all these  
> changes was to replace the internal parameter API (subject to change). We  
> should not be replacing it with just another subject-to-change API. Agree?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/pull/36#issuecomment-437709288>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsPRwWcLvQZ5Y2j2-iof4x1OIHtxPks5uuJ7WgaJpZM4YX-QU>  
> .  
>

---

## Comment 9

> Username: yet-another-user  
> Created_at: 2018-11-11 23:27:05 UTC  
> Updated_at: 2018-11-11 23:28:14 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437715454  

1. You cannot possibly say "it will"... unless you are the developer... even then the circumstances change and intentions (like including certain functionality) might never realize. That's called life :-) which we have very little control over. :-)  
2. You missed my point. My point was/is that _as of now_ you are proposing to replace one subject-to-change API (internal) with another subject-to-change API (unpublished).   
3. More so. Consider the following scenario. I incorporate the suggested changes which rely on parameter/develop. I obviously cannot merge those convert/develop changes into convert/master... as it'll break it. Agree? More so, now I cannot use/rely on these changes in my own development... because, again, I cannot merge them into convert/master... until parameter/develop has been merged into parameter/master... which is an unknown. So, all I can do at the moment is to dock the changes for later waiting for the functionality made available in parameter/master. That's what I've done so far (in my local repository). Agree?

---

## Comment 10

> Username: yet-another-user  
> Created_at: 2018-11-12 00:11:34 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437718880  

Aha, I went to parameter/develop and see that you are active in that branch. Good. Now I am more inclined to accept your (seemingly bold) "it will" statement. :-) Now, in parameter/develop I also see .travis.yml and appveyor.yml. One is "." prefixed. the other not. Is it intentional? With regard to "convert" I thought you mentioned that appveyor did not work. So, I removed appveyor.yml, right? Wrong?

---

## Comment 11

> Username: CromwellEnage  
> Created_at: 2018-11-12 01:36:17 UTC  
> Updated_at: 2018-11-12 01:36:37 UTC  
> Url: https://github.com/boostorg/convert/pull/36#issuecomment-437726685  

The naming conventions are imposed by Travis Cl and AppVeyor, so it is intentional.  
  
Some repositories run the appveyor.yml script while others don't, depending on how their owners set the repositories up.  In order to enable automated testing in my own forks, I also need to have Travis Cl and AppVeyor accounts, then add the forked projects to AppVeyor manually (whereas Travis adds the projects automatically if you use "Sign In via GitHub").  I don't know how different the setup requirements are for library maintainers: you should get a better answer from someone like @ericniebler or @pdimov who has more experience.  
  
In the meantime, I'm going to close this PR and submit a new one in order to resolve the branch conflicts.

---
