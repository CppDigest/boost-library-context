# #590 - Moving to C++11 or C++14 [Closed]

> Username: awulkiew  
> Created at: 2019-05-16 18:23:44 UTC  
> Updated at: 2021-02-11 22:37:57 UTC  
> Closed at: 2021-02-11 22:37:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/590  

I'd like to start a discussion about moving to the newer standard. Among other things it would definitely make some of the internals clearer than they are now. What do you think about it?  
  
One solution would be to simply announce that Geometry drops the support for older compilers. We should also decide which C++11 features exactly we'd like to use because the support for different features was added in various versions of different compilers. I prepared a list of the most important features I think we could use and the list of 4 compilers below. I'll update it if someone suggests that additional feature is useful.  
  
The feature I think would be the most useful is `auto` because it'd allow us to get rid of many very long template instantiations, `typedef`s, etc. So I marked the features and minimum versions of compilers based on `auto` feature below (so versions GCC 4.4, Clang 2.9, VS 2010, Intel C++ 11.0 and below). The youngest of these compilers is GCC 4.4 released in March 13, 2012. Of course we can relax it if needed, .e.g Rvalue references would also be useful and they are supported in the same versions besides Intel C++ which needs the next minor version. The next thing is I think lambda expressions but this would require GCC 4.5 and Clang 3.1 which basically means compilers released in and after 2012.  
  
| Feature | GCC | Clang | MSC/MSVC/VS | Intel C++ |  
| --------- | --------- | --------- | --------- | --------- |  
| Rvalue references                                       | **4.3** | **2.9** | **16.0/10.0/2010** | 11.1 |  
| Variadic templates                                      | **4.3** | **2.9** | 18.0/12.0/2013 | 12.1 |  
| Initializer lists                                              | **4.4** | 3.1 | 18.0/12.0/2013 | 13.0 |  
| **Static assertions**                                    | **4.3** | **2.9** | **16.0/10.0/2010** | **11.0** |  
| **auto-typed variables**                             | **4.4** | **2.9** | **16.0/10.0/2010** | **11.0** |  
| Lambda expressions                                   | 4.5 | 3.1 | **16.0/10.0/2010** | 12.0 |  
| **decltype(expression)**                             | **4.3** | **2.9** | **16.0/10.0/2010** | **11.0** |  
| Null pointer constant                                  | 4.6 | **2.9** | **16.0/10.0/2010** | 12.1 |  
| Strongly-typed enums                                | **4.4** | **2.9** | 17.0/11.0/2012 | 13.0 |  
| constexpr                                                    | 4.6 | 3.1 | 19.0/14.0/2015 | 13.0 |  
| Delegating constructors                             | 4.7 | 3.0 | 18.0/12.0/2013 | 14.0 |  
| Explicit conversion operators                      | 4.5 | 3.0 | 18.0/12.0/2013 | 13.0 |  
| Defaulted and deleted functions                | **4.4** | 3.0 | 18.0/12.0/2013 | 12.0 |  
| Range-based for                                         | 4.6 | 3.0 | 17.0/11.0/2012 | 13.0 |  
| noexcept                                                     | 4.6 | 3.0 | 19.0/14.0/2015 | 14.0 |  
| Type traits                                                   | **4.3** | 3.0 | **14.0/8.0/2005** | **10.0** |  
  
See: [cppreference](https://en.cppreference.com/w/cpp/compiler_support), [GCC](https://www.gnu.org/software/gcc/projects/cxx-status.html), [Clang](https://clang.llvm.org/cxx_status.html), [MSVC](https://docs.microsoft.com/en-us/cpp/overview/visual-cpp-language-conformance?view=vs-2019), [Intel](https://software.intel.com/en-us/articles/c0x-features-supported-by-intel-c-compiler).  
  
For context release dates of the compilers.  
  
| Compiler | Release date |  
| --------- | --------- |  
| GCC 4.3 | Jun 27, 2011 |  
| GCC 4.4 | March 13, 2012 |  
| GCC 4.5 | Jul 2, 2012 |  
| GCC 4.6 | April 12, 2013 |  
| GCC 4.7 | June 12, 2014 |  
| Clang 2.9 | April 6, 2011 |  
| Clang 3.0 | December 1, 2011 |  
| Clang 3.1 | May 22, 2012 |  
| VS 2005 | October 2005 |  
| VS 2010 | April 12, 2010 |  
| VS 2012 | August 15, 2012 |  
| VS 2013 | October 17, 2013 |  
| VS 2015 | July 20, 2015 |  
| Intel C++ 10.0 | June 5, 2007 |  
| Intel C++ 11.0 | November 2008 |  
| Intel C++ 11.1 | June 23, 2009 |  
| Intel C++ 12.0 | November 7, 2010 |  
| Intel C++ 12.1 | September 8, 2011 |  
| Intel C++ 13.0 | September 5, 2012 |  
| Intel C++ 14.0 | September 4, 2013 |

---

## Comment 1

> Username: mloskot  
> Created at: 2019-05-16 18:35:31 UTC  
> Updated at: 2019-05-16 18:38:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493183501  

### A w e s o m e!  
  
I actually was going to propose it at some point soon and offer help in the code migration.  
One thing I'd like to do is to get rid of Boost.TypeTraits and Boost.MPL, replacing the former with `<type_traits>` and the latter, where necessary`, with Boost.MP11. I did that for Boost.GIL already.   
  
I'd like to help. Please, let me know if/where I can help.  
  
### GCC 4.8  
  
IMHO, when moving to C++11, there is little point to support anything older than GCC 4.9. The GCC 4.8 is dead, see https://github.com/boostorg/gil/issues/282#issuecomment-486871878

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-05-16 18:57:30 UTC  
> Updated at: 2019-05-16 18:58:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493190995  

I was conservative in my proposal to maximize the effect with the least possible change but we can do a bigger jump.  
  
Type-traits wouldn't be a problem since we're nearly there in the initial proposal (need Clang 3.0).  
  
Regarding MP11, I didn't pay much attention, is MP11 much superior than MPL?  
  
I can't find the list of compilers or language features MP11 depends on. I see variadic templates and template aliases in the [overview](https://www.boost.org/doc/libs/develop/libs/mp11/doc/html/mp11.html) so I guess these features are required. If this is correct it means we'd need at least GCC 4.7(4.9), Clang 3.0, VS 2013 and Intel 12.1 which is not that high requirement.  
  
> I'd like to help. Please, let me know if/where I can help.  
  
Well, we have to agree about a set of features and compilers and then you are free to go with proposing anything you like :)

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-05-16 19:07:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493194182  

There is a short list in [the readme at GitHub](https://github.com/boostorg/mp11), so:  
- g++ 4.7 or later  
- clang++ 3.3 or later  
- Visual Studio 2013, 2015, 2017  
  
I wonder what do they need Clang 3.3 for and what about other compilers.

---

## Comment 4

> Username: mloskot  
> Created at: 2019-05-16 19:08:10 UTC  
> Updated at: 2019-05-16 19:09:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493194502  

> Type-traits wouldn't be a problem since we're nearly there in the initial proposal (need Clang 3.0).  
  
Often, when you get rid of TypeTraits, you need to get rid of MPL or do some plumbing.  
  
> Regarding MP11, I didn't pay much attention, is MP11 much superior than MPL?  
  
Without a doubt. It's simpler, cleaner, faster, easier on compiler.  
  
> I can't find the list of compilers or language features MP11 depends on  
  
GCC 4.9 or later (GCC 4.7, 4.8 support is limited).  
  
Talk to @pdimov  
  
>> I'd like to help. Please, let me know if/where I can help.  
>  
> Well, we have to agree about a set of features and compilers and then you are free to go with proposing anything you like :)  
  
Sure. Currently, I'm just seeing your proposal as opportunity to make some suggestions here myself, but I since I'm not involved in the core development, I'll follow your lead regarding the requirements.

---

## Comment 5

> Username: awulkiew  
> Created at: 2019-05-16 19:20:41 UTC  
> Updated at: 2019-05-16 19:46:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493198484  

> Sure. Currently, I'm just seeing your proposal as opportunity to make some suggestions here myself, but I since I'm not involved in the core development, I'll follow your lead regarding the requirements.  
  
Right, I appreciate your suggestions. I don't have a strong opinion about the minimum versions. I would have nothing against going directly to C++14. C++17 is probably not an option though I'd like to use `constexpr if`. I'm more concerned about the users. We could ask on the list but I think the majority of users is not subscribed so we have to estimate.  
  
@barendgehrels What do you think about it?

---

## Comment 6

> Username: vissarion  
> Created at: 2019-05-17 12:52:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493442351  

Thanks @awulkiew for opening this discussion!   
  
I neither have a strong opinion on minimum versions (thanks for the tables above!). It is actually a user issue, maybe @barendgehrels knows more about user needs/costraints.   
  
Also does it make sense to apply the new standard first to new features or move the whole library directly to C++11 (or later) or even support two versions?

---

## Comment 7

> Username: barendgehrels  
> Created at: 2019-05-18 16:09:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493688177  

Thanks for this. Yes, I think at some point we should move to C++11 or so.  
  
Of course I don't know all users, and I agree that we should ask the list first to see which versions of the language they are using. I know some users which are using C++11 internally - so for them this would be very welcome, but OTOH if we would go to C++14 directly, that would be a big problem: they cannot upgrade Boost anymore. This will be the case for many users.  
  
How are other Boost libraries doing this? Just drop support for C++03, or create a clone, freeze the current version ("geometry") and continue with, for example, Boost.Geometry2 ? Like we discussed in Brussels I think.  
  
Anyway, let's first ask the Geometry list to see if they like the idea, and then the Boost list to see how other libraries are handling this (or maybe you know this already).

---

## Comment 8

> Username: mloskot  
> Created at: 2019-05-18 17:18:04 UTC  
> Updated at: 2019-05-18 17:18:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493692747  

There have been numerous discussions about migrating Boost to newer versions of C++. AFAIK, there is no grand plan in place.  
  
Each library is independent here and maintainers are free to switch their libraries to newer versions, especially libraries which are not core dependencies of other Boost libraries.  
  
One quote from @pdimov that I remembered, https://lists.boost.org/Archives/boost/2018/11/244180.php  
  
```  
much of the outside world thinks of Boost as a single thing.  
Our perspective as library maintainers is that libraries are  
independent  
```

---

## Comment 9

> Username: awulkiew  
> Created at: 2019-05-18 18:18:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493696754  

> I know some users which are using C++11 internally - so for them this would be very welcome, but OTOH if we would go to C++14 directly, that would be a big problem  
  
I think it's more about specific compilers than a standard because various compilers implement certain sets of features. Standard is theoretical definition of features, compiler version is practical (+compiler bugs). E.g. if they are using GCC 4.9 or later in their company they could use some of the C++14 features already assuming it's not against their guidelines to compile with `-std=c++14`. But yes, I agree it would be a big jump. Though I'd like to use return type deduction, generic lambdas and variable templates but it would require GCC-5 (2015), Clang-3.4 (2014) or VS 2015 and I think this requirement would be too high. But I'm flexible. ;)  
  
> Just drop support for C++03, or create a clone, freeze the current version...  
  
The question is: would significant number of users want to use C++03 Geometry and at the same time other libraries further developed in future versions of Boost? In that case we could consider maintaining both versions of Geometry at the same time and by maintaining I understand making sure it compiles with future versions of other libraries, so no bug fixes or new features. Otherwise we could simply bump the requirements and force users who want to use older Geometry to use older versions of other libraries as well. If really needed these users would be forced to marry new Boost with old Geometry and resolve problems by themselves.  
  
> ...("geometry") and continue with, for example, Boost.Geometry2 ?  
  
I'd rather freeze the current version as Geometry03, GeometryOld or something like that and simply move forward with Geometry (like SpiritClassic and Spirit).

---

## Comment 10

> Username: mloskot  
> Created at: 2019-05-18 19:21:03 UTC  
> Updated at: 2019-09-30 17:41:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493700844  

To me, Spirit Classic is different as it's a maintained line of 1.8.x to some extent.  
So, it's not frozen version.  
Do you expect to maintain the two lines of Geometry?   
  
Isn't it enough to freeze implicitly via Boost release?  
For example,   
- Geometry in Boost <= 1.75 is C++03  
- Geometry since 1.75 requires C++11 compiler  
  
I'd argue that it's important to label library with minimal required  C++ standard version rather than relying on selection of C++ features.   
The latter would open the library for mixture of features from different C++ versions - quite a mess, especially from user point of view.

---

## Comment 11

> Username: awulkiew  
> Created at: 2019-05-18 21:48:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493709639  

> Do you expect to maintain the two lines of Geometry?  
  
If really necessary but I'd prefer to avoid that.  
  
> Isn't it enough to freeze implicitly via Boost release?  
  
I have nothing against it. :)  
  
> The latter would open the library for mixture of features from different C++ versions - quite a mess, especially from user point of view.  
  
I agree. The features mentioned here are only for us to decide what we'd like to do. In addition to explicitly stating that we require C++11 (but only as general requirement) I'd suggest explicitly labeling library by specific versions of compilers. This way from user perspective everything would be even more clear since they wouldn't have to know which version supports all features from the standard and at the same time we would be able to pick earlier version than the one supporting all features if we didn't need all of them. Just like MP11 depending on GCC 4.7. Yes I know the support is partial but maybe we don't need all of it.  
  
Furthermore if we agreed to your suggestion about GCC 4.9 we would still have to state this compiler version is required because technically all C++11 features were implemented in GCC 4.8.1. So there is no running from compiler versions supporting features in ways satisfying our needs.  
  
Compiler version is more practical than C++11 standard.

---

## Comment 12

> Username: barendgehrels  
> Created at: 2019-05-19 09:09:11 UTC  
> Updated at: 2019-05-19 09:09:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493739971  

> AFAIK, there is no grand plan in place.  
  
I know, and I know we can decide for our own - but what happened in practice for other libraries?  
  
> I think it's more about specific compilers than a standard  
> Compiler version is more practical than C++11 standard.  
  
Of course compilers are important, but they follow a standard and users usually follow a standard. The standard is specified in makefiles / when compiling. Organizations where I've worked for are often flexibel in compilers (some use clang, some use gcc - for some OS you use other compilers, with the same codebase) but are not flexibel in standard. And indeed sometimes only a specific subset of C++11 features are allowed to be used, to still support some older versions of compilers.  
  
> assuming it's not against their guidelines to compile with -std=c++14  
  
I'm talking about large organizations, with 100s of developers on one codebase. They prepare these upgrades for months. You cannot specify a new standard overnight without breaking several things at the same time. The same, indeed, holds for compilers versions.  
  
> I'd rather freeze the current version as Geometry03  
  
Good idea, I would prefer that indeed over Geometry2 - besides that, freezing a library would be an option for me personally - but I don't know if that holds for all our users. The drawback of renaming the current version is that current users have to adapt their includes, so it still requires some effort for them.  
  
> Do you expect to maintain the two lines of Geometry?  
  
Complete maintenance will require too much effort. But just porting some obvious bugs on request might be an option. Therefore I would prefer to upgrade after all rescaling has been removed. That takes me quite some time already, sorry for that, but the end is visible now.

---

## Comment 13

> Username: mloskot  
> Created at: 2019-05-19 15:11:08 UTC  
> Updated at: 2019-05-19 15:11:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493765259  

@barendgehrels   
> > AFAIK, there is no grand plan in place.  
>  
> I know, and I know we can decide for our own - but what happened in practice for other libraries?  
  
Approaches are different. For example  
  
* [coroutine is C++03 and coroutine2 is C++11](https://github.com/boostorg/coroutine/blob/master/README.md)  
  
* GIL since Boost 1.68 requires C++11 and there is no GIL2 or GIL3. The README just says which Boost version is required now and which for old compiler. Obviously, GIL from Boost <1.68 does not offer new features that are being added to GIL in Boost >=1.68.  
  
There are libraries that require C++11 or C++14 since their beginning.  
  
@awulkiew   
> if we agreed to your suggestion about GCC 4.9 we would still have to state this compiler version  
> is required because technically all C++11 features were implemented in GCC 4.8.1.   
  
Yes. What I'm trying to explain is that even you pin at GCC 4.8 based on the list of C++ features it supports, you may be forced to remove 4.8 from the list because some features will simply not work due to bugs. Those bugs in the GCC 4.8 and other old compilers/versions will not be fixed.

---

## Comment 14

> Username: barendgehrels  
> Created at: 2019-05-19 16:58:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-493773418  

@mloskot  thank you!

---

## Comment 15

> Username: vissarion  
> Created at: 2019-05-21 11:15:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-494348416  

I lean against @mloskot's proposal about supporting a minimal required C++ standard version. Then we can also comment on how this is implemented in various compilers, to help users.

---

## Comment 16

> Username: tinko92  
> Created at: 2019-10-02 13:48:32 UTC  
> Updated at: 2019-10-02 14:26:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-537500547  

I hope this is not out of scope for this conversion, but I would be interested in the position with regard to a matter related to language versions other than the minimum required version.  
  
Is there a policy in Boost.Geometry with regard to contributions that require newer language features but do not break the build for compilation with older language versions? For example, in this file https://github.com/boostorg/geometry/blob/8eee20af4df5fb826c7a4860bebabfee8ac4f588/include/boost/geometry/geometries/linestring.hpp#L78  a C++11-dependend feature is provided, but if C++11 is not available, I guess that BOOST_NO_CXX11_HDR_INITIALIZER_LIST will prevent compilation issues.  
Would, similarly Pull Requests be acceptable that optionally make functions constexpr that would only be allowed to be constexpr since C++14 or that provide C++20 concepts for better compiler error messages but conditional on some similar macros like !BOOST_NO_CXX14_CONSTEXPR or !BOOST_NO_CXX20_CONCEPTS (last name made up)?  
  
Edit: I can see that an issue with that might be that bugs in such conditional segments could slip through the tests done by CI if different language settings are not tested automatically.

---

## Comment 17

> Username: mloskot  
> Created at: 2019-10-02 17:39:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-537602296  

@tinko92 replying to your insight from [Gitter on October 2, 2019 3:54 PM](https://gitter.im/boostorg/geometry?at=5d94ac2efb131014e72745ad):  
> I was thinking about something that improves error messages if C++20 is available but doesn't break things if it isn't.   
  
Generally speaking, I think your approach is valid and worth considering.  
I think C++20 concepts are very good candidates to introduce into a library as non-intrusive C++20 features, without introducing C++20 as requirement.  
I'm going to start using C++20 constraints and concepts in Boost.GIL as opt-in feature only while keeping the library C++11. I'm hoping C++20 concepts will help to find issues and increase library quality.  
  
If Boost.Geometry will allow similar approach is yet to be discussed, I think.  
Although I nowadays play a rather minor supportive role in Boost.Geometry, I still have TODO list which I'm hoping to contribute or help about, and C++ modernisation tasks are on the list.

---

## Comment 18

> Username: awulkiew  
> Created at: 2019-10-02 20:44:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-537672424  

@tinko92 Conditionally enabled features from future standards are of course welcome. There are some places where this is already done in the library, e.g. models, index and transformations. We just have to make sure that the usefulness of a feature is greater than the cost of maintaining more complex code.  
  
Prefer solutions that are close to the native language. E.g. consider C++14 `constexpr`. Putting macro checks around each function call is a worse solution than defining a macro expanded to `conxtexpr` with C++14 compiler or to nothing with older compilers, similar to `BOOST_CONSTEXPR`. If you need to define new macros you may put them in `boost/geometry/core/config.hpp`.  
  
Furthermore if you think that some macro could be used in other libraries too you could consider proposing an inclusion to Boost.Config.

---

## Comment 19

> Username: mloskot  
> Created at: 2020-03-25 12:19:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-603808241  

@awulkiew Your https://github.com/boostorg/geometry/issues/686#issuecomment-603800171 made me wonder if one of baby steps could be adding `BOOST_CONSTEXPR` to the math utilities (to pick constexpr-ed `std::max`, etc. when using modern compilers). What do you think?

---

## Comment 20

> Username: awulkiew  
> Created at: 2020-03-25 14:38:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-603876172  

@mloskot Yes, that's a good idea. In general modernizing math. `constexpr` could also be added to point, box and segment models. Move emulation could be removed in the R-tree. Another starting point could be our range utilities which currently doesn't use rval references.  
  
But I think the first thing we should do is to decide how we will handle this change. We should announce it for sure but first decide which version will be the first one requiring the newer standard and which standard exactly we're targeting C++11 or 14. The former is probably more realistic. ;)

---

## Comment 21

> Username: awulkiew  
> Created at: 2020-03-25 17:06:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-603965321  

@mloskot I think I misunderstood you earlier. Adding `BOOST_CONSTEXPR` or `BOOST_CXX14_CONSTEXPR` could be added even without C++11 requirement. It is of course welcome but I think we should go straight to proper C++11 or C++14.

---

## Comment 22

> Username: mloskot  
> Created at: 2020-03-25 17:57:17 UTC  
> Updated at: 2020-03-25 18:49:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-603992926  

@awulkiew Yes, my comment assumed no change to required C++ version yet, then `BOOST_CONSTEXPR` and others could come handy.   
  
> I think we should go straight to proper C++11 or C++14.  
  
Yes, me too, I sign the switch to newer C++ with all my paws :)  
  
> (...) which standard exactly we're targeting C++11 or 14. The former is probably more realistic. ;)  
  
Yes, I also think C++11 is the feasible step forward.  
  
> I think the first thing we should do is to decide how we will handle this change.  
  
I have just asked the Boost mailing list about basic recommendations [[geometry] Planning switch to C++11](https://lists.boost.org/Archives/boost/2020/03/248500.php) and as answer received pointer to this excellent article on [Phasing out C++03 Support in Boost](https://pdimov.github.io/articles/phasing_out_cxx03.html) by Peter Dimov.   
  
I think the "Suggested Policy" is a reasonable approach.  
Given the document is not official Boost policy, AFAIU, and the policy is a suggestion, I'm not sure how strict we want to be about this requirement:  
  
```  
At least two Boost releases must ship with a deprecation notice before support is dropped.  
```  
  
/cc @barendgehrels @vissarion @awulkiew ☝️

---

## Comment 23

> Username: barendgehrels  
> Created at: 2020-03-25 18:49:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-604020833  

So I repeat here my comment of last year:  
  
"Therefore I would prefer to upgrade after all rescaling has been removed."  
  
We just have to wait a bit more.

---

## Comment 24

> Username: barendgehrels  
> Created at: 2020-03-25 18:50:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-604021489  

> I think we should go straight to proper C++11 or C++14  
  
And we probably can go to C++14 then.

---

## Comment 25

> Username: mloskot  
> Created at: 2020-03-25 18:52:36 UTC  
> Updated at: 2020-03-25 18:54:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-604022413  

@barendgehrels Then perhaps it's the right time to add the deprecation message to e.g.  in `boost/geometry/geometry.hpp` or  `boost/geometry/geometries/point.hpp` and release it with the upcoming Boost 1.73  
  
```  
#include <boost/config.hpp>  
#include <boost/config/pragma_message.hpp>  
  
#if defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) || defined(BOOST_NO_CXX11_RVALUE_REFERENCES) || defined(BOOST_NO_CXX11_HDR_MEMORY)  
BOOST_PRAGMA_MESSAGE("C++03 support is deprecated in Boost.Geometry 1.73 and will be removed in or after Boost.Geometry 1.75.")  
#endif  
```  
  
as per Peter's [Suggested Policy](https://pdimov.github.io/articles/phasing_out_cxx03.html#_suggested_policy).

---

## Comment 26

> Username: barendgehrels  
> Created at: 2020-03-25 19:09:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-604030989  

@mloskot :  this message sounds in general fine to me and does not restrict us to a specific version.   
Thanks.  
  
The only thing I doubt is the wording `deprecated in ... 1.73` because it is obviously still there in all the code. But if this is the std/recommendation, then it is fine to me.

---

## Comment 27

> Username: pdimov  
> Created at: 2020-03-25 19:34:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-604042856  

The Standard meaning of "deprecated" is exactly that, still there in the code, but will be removed at some later point. http://eel.is/c++draft/depr#2

---

## Comment 28

> Username: awulkiew  
> Created at: 2020-03-26 01:42:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-604179424  

@barendgehrels out of curiosity, why do you prefer to wait with it until the rescaling is removed? After announcing the change in the requirement of the C++ standard we wouldn't rewrite the library. In particular we could agree that we do not touch parts that you're working on. I think that for you nothing would change besides the fact that you would be able to use newer C++ while working on rescaling. Am I missing something?

---

## Comment 29

> Username: barendgehrels  
> Created at: 2020-03-26 21:42:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-604702299  

@awulkiew you are right, I first thought we would freeze a version, but that is not necessary and then waiting is not necessary. Thanks.

---

## Comment 30

> Username: mloskot  
> Created at: 2020-03-27 11:24:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-604948595  

Following https://github.com/boostorg/geometry/issues/590#issuecomment-604022413 I have submitted proposal with the deprecation announcement in https://github.com/boostorg/geometry/pull/689. It needs to be refined after at least these two are agreed upon:  
  
1. Which C++ version we switch to, C++11 or to C++14?  
  
   We seem to have already agreed that C++17 is not an option.  
   We seem to have already agreed that C++14 is a good choice.  
  
2. Which Boost version, 1.74 or 1.75 or later, will require the later C++ version  
  
    We seem to be keen in making the switch sooner than later, but perhaps 1.75 is a good round number :)

---

## Comment 31

> Username: barendgehrels  
> Created at: 2020-03-28 17:10:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-605490201  

* `We seem to have already agreed that C++14 is a good choice.` -> I agree  
* `Perhaps 1.75 is a good round number` -> I agree

---

## Comment 32

> Username: vissarion  
> Created at: 2020-03-28 18:34:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-605501103  

>* `We seem to have already agreed that C++14 is a good choice.` -> I agree  
>* `Perhaps 1.75 is a good round number` -> I agree  
  
+1

---

## Comment 33

> Username: mloskot  
> Created at: 2020-03-28 20:24:55 UTC  
> Updated at: 2020-03-28 20:25:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-605514817  

@barendgehrels @vissarion I adjusted the deprecation message in #689   
@awulkiew Do you agree with that too?  
  
The only reason I'm a bit rushing this deprecation notice is that we are getting close to Boost 1.73 closure.

---

## Comment 34

> Username: awulkiew  
> Created at: 2020-03-28 23:14:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-605532158  

@mloskot Yes, I agree, thank you.  
  
Btw, this is not a major change so I think we have time till Apr 8 or maybe even after beta is released.

---

## Comment 35

> Username: StefanBruens  
> Created at: 2021-01-24 11:26:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-766331975  

Would it be possible to add a 'Boost::geometry' CMake target, with `target_compile_features(Boost::geometry PUBLIC cxx_std_14)` set?  
  
https://cmake.org/cmake/help/latest/manual/cmake-compile-features.7.html#requiring-language-standards

---

## Comment 36

> Username: mloskot  
> Created at: 2021-01-24 13:03:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-766344655  

@StefanBruens Please, make a feature request by opening a separate issue and I will try to implement it.  
  
There is Boost-wide preliminary CMake support (e.g. `boostdep --cmake <lib>` can generate `CMakeLists.txt` files that define Boost targets in unified way. The `b2 install` mode can also deploy those CMake configuration files. I also think it would be good to use/add support for this work in Boost.Geometry

---

## Comment 37

> Username: jwakely  
> Created at: 2021-01-25 12:35:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-766786462  

I'm happy to see Boost libs dropping support for ancient versions of C++, but shouldn't the C++14 requirement be listed at https://www.boost.org/users/history/version_1_75_0.html ?

---

## Comment 38

> Username: mloskot  
> Created at: 2021-01-25 20:05:24 UTC  
> Updated at: 2021-01-25 20:59:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-767079835  

@jwakely Thank you for pointing it out. I've submitted the post-factum update in https://github.com/boostorg/website/pull/590 and @glenfe kindly offered help in re-generating the HTML page on the website.

---

## Comment 39

> Username: jwakely  
> Created at: 2021-01-25 20:57:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/590#issuecomment-767107616  

Excellent, thanks!
