# #351 - support Microsoft Visual C++ Compiler 15.0 [Closed]

> Username: vincent-hui  
> Created at: 2017-05-27 13:49:43 UTC  
> Updated at: 2018-10-01 15:40:22 UTC  
> Closed at: 2017-05-29 22:08:08 UTC  
> Url: https://github.com/boostorg/hana/issues/351  

Can hana supports Microsoft Visual C++ Compiler 15.0 which is installed by Visual Studio 2017

---

## Comment 1

> Username: MarcelRaad  
> Created at: 2017-05-28 12:11:02 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-304509808  

This is not trivial, unfortunately. I've started to look into what changes would be required to get the tests and examples to at least compile here:  
https://github.com/MarcelRaad/hana/tree/msvc  
  
But besides the known limitations, there are a lot of ICE's which mostly disappeared when trying to create minimal repros :-(

---

## Comment 2

> Username: ldionne  
> Created at: 2017-05-29 22:08:08 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-304737006  

Short answer is no, it's not possible to do it. More information at #334, of which this is a duplicate.

---

## Comment 3

> Username: Bulat-Ziganshin  
> Created at: 2018-01-10 07:16:16 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-356519762  

Can you reinvestigate that since MSVC made two compiler updates - in 2017.3 and 2017.5 versions. Also, i propose to clearly document which MSVC versions DOESN'T work since wiki page wasn't updated since 2016, giving false feeling that recent MSVC versions weren't yet tested at all

---

## Comment 4

> Username: ldionne  
> Created at: 2018-01-10 07:21:21 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-356520614  

I don't have local access to MSVC and I've personally never tested on MSVC. If you care about recent MSVCs and have access to it locally, I would greatly appreciate if you could try building and report the results here (so I can edit the wiki) or on the wiki directly.

---

## Comment 5

> Username: MarcelRaad  
> Created at: 2018-01-10 10:06:44 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-356556827  

I'm still testing it from time to time. The situation has improved with Update 6 Preview 1, but there are still a few hundred compile errors in the examples and tests even with the above workarounds (down from a few thousands in 2017 RTM). The compute_info call in group.hpp still ICEs, which affects multiple examples and tests.

---

## Comment 6

> Username: vincent-hui  
> Created at: 2018-01-10 13:58:16 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-356609445  

@MarcelRaad did you try Clang in Visual Studio 2017? https://blogs.msdn.microsoft.com/vcblog/2017/03/07/use-any-c-compiler-with-visual-studio/

---

## Comment 7

> Username: MarcelRaad  
> Created at: 2018-01-10 14:40:10 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-356621409  

@vincent-hui Yes, both clang-cl and the last version of clang/c2 work perfectly.

---

## Comment 8

> Username: rdilipk  
> Created at: 2018-04-18 17:38:15 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-382468938  

Its is extremely unclear exactly what version of MSVC can support building Hana. I have VS 2017 update 15.6.6 which is the latest and the greatest. It does seem to have support for variable templates flagged in this piece: <https://github.com/boostorg/hana/wiki/General-notes-on-compiler-support> as the reason why MSVC is not supported. The only drawback is MSVC still doesn't report the value of __cplusplus correctly but beyond that I get a whole bunch of errors and its hard to tell exactly which aspect of the language feature is lacking that makes it impossible to build in MSVC.  
  
Does anyone know?

---

## Comment 9

> Username: ricejasonf  
> Created at: 2018-04-18 17:56:27 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-382474519  

It would be interesting to see this error list. I don't use MSVC.

---

## Comment 10

> Username: rdilipk  
> Created at: 2018-04-18 18:03:44 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-382476820  

Here is a sample list of errors from index_if.hpp:  
  
    include\boost\hana\index_if.hpp(80): error C2146: syntax error: missing  
'>' before identifier 'value'  
    include\boost\hana\index_if.hpp(83): note: see reference to class  
template instantiation  
'boost::hana::index_if_impl<Tag,boost::hana::when<boost::hana::Foldable<T>::value>>'  
being compiled  
    include\boost\hana\index_if.hpp(80): error C2993: 'unknown-type':  
illegal type for non-type template parameter 'N'  
    include\boost\hana\index_if.hpp(81): error C2993: 'unknown-type':  
illegal type for non-type template parameter 'N'  
    include\boost\hana\index_if.hpp(81): error C2144: syntax error:  
'unknown-type' should be preceded by ';'  
    include\boost\hana\index_if.hpp(81): error C2238: unexpected token(s)  
preceding ';'  
    include\boost\hana\index_if.hpp(82): error C2988: unrecognizable  
template declaration/definition  
    include\boost\hana\index_if.hpp(82): error C2059: syntax error: '<end  
Parse>'  
    include\boost\hana\index_if.hpp(82): error C2334: unexpected token(s)  
preceding '{'; skipping apparent function body  
  
I believe our boost version is 1.66.0.  
  
On Wed, Apr 18, 2018 at 1:56 PM, Jason Rice <notifications@github.com>  
wrote:  
  
> It would be interesting to see this error list. I don't use MSVC.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/hana/issues/351#issuecomment-382474519>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AEXh62HmicodtbWSlP7g0xlHDg2AH4Owks5tp35NgaJpZM4NoW1L>  
> .  
>

---

## Comment 11

> Username: ldionne  
> Created at: 2018-04-18 18:28:23 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-382484365  

MSVC does not support Hana, period. They have Hana as part of their test cases, and I expect they will report it explicitly when they get Hana to build.

---

## Comment 12

> Username: rdilipk  
> Created at: 2018-04-18 18:29:24 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-382484670  

Ok. That's far more assertive. Thanks Louis! Guess I will stick with Fusion  
and MPL for the time being.  
  
On Wed, Apr 18, 2018 at 2:28 PM, Louis Dionne <notifications@github.com>  
wrote:  
  
> MSVC does not support Hana, period. They have Hana as part of their test  
> cases, and I expect they will report it explicitly when they get Hana to  
> build.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/hana/issues/351#issuecomment-382484365>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AEXh6zoOKNce7plSzqSZ1Kn80z-kRhwtks5tp4XKgaJpZM4NoW1L>  
> .  
>

---

## Comment 13

> Username: debugx  
> Created at: 2018-09-24 06:10:12 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-423883690  

Why MSVC does not support Hana?  
I tryed to build one simple project which uses Hana in VS2015 and VS2017.  
There are a lot of errors.  
In VS2017 with all last updates the following errors appeared:  
boost\hana\index_if.hpp(80): warning C4346: 'boost::hana::Foldable<T>::value': dependent name is not a type  
boost\hana\index_if.hpp(80): note: prefix with 'typename' to indicate a type  
boost\hana\index_if.hpp(83): note: see reference to class template instantiation 'boost::hana::index_if_impl<Tag,boost::hana::when<boost::hana::Foldable<T>::value>>' being compiled  
boost\hana\index_if.hpp(80): error C2146: syntax error: missing '>' before identifier 'value'  
boost\hana\index_if.hpp(80): error C2993: 'unknown-type': illegal type for non-type template parameter 'N'  
boost\hana\index_if.hpp(81): error C2993: 'unknown-type': illegal type for non-type template parameter 'N'  
boost\hana\index_if.hpp(81): error C2144: syntax error: 'unknown-type' should be preceded by ';'  
boost\hana\index_if.hpp(81): error C2238: unexpected token(s) preceding ';'  
boost\hana\index_if.hpp(82): error C2988: unrecognizable template declaration/definition  
boost\hana\index_if.hpp(82): error C2059: syntax error: '<end Parse>'  
boost\hana\index_if.hpp(82): error C2334: unexpected token(s) preceding '{'; skipping apparent function body  
boost\hana\detail\unpack_flatten.hpp(58): error C2146: syntax error: missing '>' before identifier 'value'  
boost\hana\detail\unpack_flatten.hpp(59): error C3545: 'Lengths': parameter pack expects a non-type template argument  
boost\hana\detail\hash_table.hpp(50): error C2146: syntax error: missing ';' before identifier 'type'  
boost\hana\detail\hash_table.hpp(52): note: see reference to class template instantiation 'boost::hana::detail::find_indices<Map,Key>' being compiled  
boost\hana\detail\hash_table.hpp(50): error C2208: 'boost::hana::type': no members defined using this type  
boost\hana\detail\hash_table.hpp(116): error C2146: syntax error: missing ';' before identifier 'type'  
boost\hana\detail\hash_table.hpp(118): note: see reference to class template instantiation 'boost::hana::detail::bucket_insert<boost::hana::detail::hash_table<Buckets...>,Key,Index,true>' being compiled  
boost\hana\detail\hash_table.hpp(116): error C2208: 'boost::hana::type': no members defined using this type  
boost\hana\detail\hash_table.hpp(123): error C2146: syntax error: missing ';' before identifier 'type'  
boost\hana\detail\hash_table.hpp(125): note: see reference to class template instantiation 'boost::hana::detail::bucket_insert<boost::hana::detail::hash_table<Buckets...>,Key,Index,false>' being compiled  
boost\hana\detail\hash_table.hpp(123): error C2208: 'boost::hana::type': no members defined using this type  
boost\hana\detail\hash_table.hpp(140): error C2146: syntax error: missing '>' before identifier 'type'  
boost\hana\detail\hash_table.hpp(142): note: see reference to class template instantiation 'boost::hana::detail::make_hash_table<KeyAtIndex,N,std::integer_sequence<::size_t,i...>>' being compiled

---

## Comment 14

> Username: ldionne  
> Created at: 2018-09-24 21:21:45 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-424129522  

@debugx Please file a new issue with a minimal example reproducing your issue and provide the version of the compiler you are using.

---

## Comment 15

> Username: Bulat-Ziganshin  
> Created at: 2018-10-01 15:40:22 UTC  
> Url: https://github.com/boostorg/hana/issues/351#issuecomment-425955087  

>Why MSVC does not support Hana?  
  
@debugx  With 15.7 version, MS announced full support for C++17, but now they report that they still have some bugs in the implementation. Hana is extremely extensive user of new C++ features, so its code employs some features still breaking 15.7.   
  
Since MSVC team includes Hana in the list of libraries compiled to check C++17 compatibility of MSVC, I believe that they will fix these bugs ASAP. So, just subscribe to this Issue and wait...
