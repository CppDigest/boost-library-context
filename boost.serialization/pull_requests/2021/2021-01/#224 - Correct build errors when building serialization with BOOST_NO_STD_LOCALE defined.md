# #224 Correct build errors when building serialization with BOOST_NO_STD_LOCALE defined [Merged]

> Username: nevilad  
> Created at: 2021-01-08 09:58:15 UTC  
> Updated at: 2023-04-30 18:42:14 UTC  
> Merged at: 2021-01-19 21:13:51 UTC  
> Closed at: 2021-01-19 21:13:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/224  

basic_text_iprimitive.ipp and basic_text_oprimitive.ipp have obvious errors.  
Some files included boost/archive/detail/utf8_codecvt_facet.hpp and their build ended with `wide char i/o not supported on this platform` error. This is due to this statement  
```  
#ifdef BOOST_NO_STD_WSTREAMBUF  
#error "wide char i/o not supported on this platform"  
#endif  
```  
  
BOOST_NO_STD_WSTREAMBUF is explicitly defined when BOOST_NO_STD_LOCALE is defined:  
```  
//  
// We can't have a working std::wstreambuf if there is no std::locale:  
//  
#  if defined(BOOST_NO_STD_LOCALE) && !defined(BOOST_NO_STD_WSTREAMBUF)  
#     define BOOST_NO_STD_WSTREAMBUF  
#  endif  
```  
  
The solution is to remove all these files from build when BOOST_NO_STD_LOCALE is defined. In case of serialization lib 2 files are removed, in case of wserialization the entire module.  
I've changed only the jam build, not cmake. I think cmake build will fail.

---

## Comment 1

> Username: robertramey  
> Created_at: 2021-01-18 05:55:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-762000987  

This looks like a real improvement.  Thanks for spending the time on this.  
  
My normal procedure when I see something like this is to do the "squash & merge" (into develop).  Then update my local copy and rerun the tests on the compilers that I have locally.  This gives me confidence that nothing has been overlooked. If it turns out that I discover that merging wasn't such a great idea after all, I have to back out the original merge - something that I'm loath to do as it requires one interact with git which is pretty complex in my view.  
  
Ideally I'd like to just merge this into a branch on the original repo.  But I haven't invested the effort to figure out a good workflow.  And since it doesn't happen all the time, this scheme generally works pretty well.  
  
Before doing this with your request I have a few questions:  
  
I notice that a tests are failing on a number of configurations.  Note that on the [Boost Test Matrix] (https://www.boost.org/development/tests/develop/developer/serialization.html) There are no failing tests.  Could invest age these failures in with your changes?  
  
I notice that you've made Jamfile.v2 significantly more elaborate.  And it was pretty elaborate to start with!  I've found b2 to be a nightmare.  Actually that's too harsh.  Once one has it working, it seems great - until the b2 developers decide to "fix" something.  The b2 developers develop right on the develop branch so any time they make a mistake - it ripples through the whole boost test suite.  They don't have an independent test suite for b2.  So we're stuck finding their mistakes - I hate this.  
How did you find the information in order to make your changes to b2.  Do you have confidence in those changes?  
  
I'd also be curious as to how you came upon this issue.  It's likely existed in some form for 17 years now and looks like your the first one to notice it.  
  
Again, thanks for your efforts.

---

## Comment 2

> Username: robertramey  
> Created_at: 2021-01-18 05:57:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-762001692  

I just looked into the test failures - they're all due to lack of disk space so looks like your off the hook on this part.

---

## Comment 3

> Username: nevilad  
> Created_at: 2021-01-20 15:27:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-763709671  

>Could invest age these failures in with your changes?  
  
Windows builds failed due to disk exhaustion. Posix build failed with many different errors. One of them is   
```  
// It can make no sense to assign tracking behavior to a class that  
// is not serializable. Should fail during compile.  
BOOST_CLASS_TRACKING(A, boost::serialization::track_never)  
```  
the comments state it should fail.  
Other reason is wrong class std::allocator<A> declarations. Looks like a problem in STL.  
These build errors doesn't look like the result of this fix, but to be sure these must be rebuilt with enough disk space.  
  
>How did you find the information in order to make your changes to b2. Do you have confidence in those changes?  
  
I've read b2 docs to understand the general concept, than found examples of modifying build options in other boost bjam files, builded serialization with and without BOOST_NO_STD_LOCALE. So yes, I'm confident.  
  
>I'd also be curious as to how you came upon this issue.  
  
I've added serialization to some legacy code that uses BOOST_NO_STD_LOCALE. Before serailization usage this project didn't use any boost libraries that require their build.

---

## Comment 4

> Username: robertramey  
> Created_at: 2023-04-28 18:21:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-1527931953  

I've been having some issues which I've tracked down to this change.  So I have a few questions.  
  
BOOST_NO_STD_WSTREAMBUF is explicitly defined when BOOST_NO_STD_LOCALE is defined.  It seems that this is the source of the perceived need for the fix.  
  
I don't know where this is explicitly defined, but it seems to me that this is an error.  I see no reason why wstream could not be used even is standard local is not defined. I'm still looking into this.  
  
This is creating issues which show up with CMake build which I'm trying to fix.

---

## Comment 5

> Username: robertramey  
> Created_at: 2023-04-28 21:58:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-1528134007  

I'm having second thoughts about this.  
  
_basic_text_iprimitive.ipp and basic_text_oprimitive.ipp have obvious errors._  
  
I'm not seeing any error here.   
  
#ifdef BOOST_NO_STD_WSTREAMBUF  
#error "wide char i/o not supported on this platform"  
#endif  
  
Seems to me that this is correct behavior.  What's the problem with this error message.  It seems to indicate that the wide char archive is being used with a system which doesn't support wide character i/o.  So it errors out at compilation time - which is the desired behavior.  
  
To my mind, this change wouldn't change anything.  But it seems that I have an issue with the CMake version of the library build.  I think there might be an error in here somewhere, but I don't think this is it.  I'm inclined to back out this change.  If you have more information which explain what I'm missing here, I'd be pleased to look at it.  
  
BTW - congratulations on figuring out the whole thing - including the B2 scripts.  Not a trivial task.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-04-29 08:13:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-1528713357  

@nevilad what is the specific situation (compiler/platform) that caused the original issue?  
  
@robertramey I'm not sure this is the correct fix - the Jamfile fix looks for -DBOOST_NO_STD_LOCALE *on the command line* and disables the wide character build if it's found.  That's not really how this is supposed to work, and I'd be interested to know why anyone is ever defining that on the command line anyway. @nevilad ?  
  
There is a much simpler way to disable wide character builds in this situation: Boost.Config has built in support for build time configuration, and that would take care of BOOST_NO_STD_LOCALE being automatically defined by Boost.Config.  I'll file a PR when I have a minute, but I'd still like to hear from the OP for the rationale for this.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2023-04-29 12:39:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-1528779076  

This PR simplifies all this, and allows the tests to run when there is no std::streambuf: https://github.com/boostorg/serialization/pull/282

---

## Comment 8

> Username: nevilad  
> Created_at: 2023-04-29 15:57:37 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-1528818178  

>I'm having second thoughts about this.  
>basic_text_iprimitive.ipp and basic_text_oprimitive.ipp have obvious errors.  
>I'm not seeing any error here.  
  
The code before my patch:  
```  
    precision_saver(is_),  
#ifndef BOOST_NO_STD_LOCALE  
    codecvt_null_facet(1),  
    archive_locale(is.getloc(), & codecvt_null_facet),  
    locale_saver(is)  
{  
    if(! no_codecvt){  
        is_.sync();  
        is_.imbue(archive_locale);  
    }  
    is_ >> std::noboolalpha;  
}  
#else  
{}  
#endif  
  
```  
  
With BOOST_NO_STD_LOCALE defined it became:  
```  
    precision_saver(is_),  
{}  
  
```  
  
The obvious error is the comma after `precision_saver(is_)`.

---

## Comment 9

> Username: nevilad  
> Created_at: 2023-04-29 16:12:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-1528821247  

>@nevilad what is the specific situation (compiler/platform) that caused the original issue?  
  
Legacy code, compiler - Visual Studio,  platform - Windows.

---

## Comment 10

> Username: nevilad  
> Created_at: 2023-04-30 18:10:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-1529101563  

> I'd still like to hear from the OP for the rationale for this.  
  
1) A legacy project had BOOST_NO_STD_LOCALE defined. It is unknown what exactly was wrong without this define. The define was added because googling of the problem advised to use it as the solution.  
2) I've added serialization to this project. basic_text_iprimitive.ipp and basic_text_oprimitive.ipp didn't compile. I corrected these.  
3) Then сompilation resulted in error "wide char i/o not supported on this platform" from boost\archive\detail\utf8_codecvt_facet.hpp:  
```  
#ifdef BOOST_NO_STD_WSTREAMBUF  
#error "wide char i/o not supported on this platform"  
#endif  
```  
  
I've found that BOOST_NO_STD_WSTREAMBUF is explicitly defined when BOOST_NO_STD_LOCALE is defined:  
  
```  
//  
// We can't have a working std::wstreambuf if there is no std::locale:  
//  
#  if defined(BOOST_NO_STD_LOCALE) && !defined(BOOST_NO_STD_WSTREAMBUF)  
#     define BOOST_NO_STD_WSTREAMBUF  
#  endif  
```  
  
Thus my solution was not to compile the files, that had this error if BOOST_NO_STD_LOCALE is defined. I was not aware how to do it, searched in other jam files and came to this solution.

---

## Comment 11

> Username: nevilad  
> Created_at: 2023-04-30 18:42:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/224#issuecomment-1529112167  

>BOOST_NO_STD_WSTREAMBUF is explicitly defined when BOOST_NO_STD_LOCALE is defined. It seems that this is the source of the perceived need for the fix.  
  
Yes, this is the reason of jamfile fix.  
  
>I don't know where this is explicitly defined, but it seems to me that this is an error. I see no reason why wstream could not be used even is standard local is not defined. I'm still looking into this.  
  
I've didnt analyze if wstreambuf can be used when there is no locale. I've made my fix assuming it cant, i.e. the #error directive was correct.  
  
>Seems to me that this is correct behavior. What's the problem with this error message. It seems to indicate that the wide char archive is being used with a system which doesn't support wide character i/o. So it errors out at compilation time - which is the desired behavior.  
>To my mind, this change wouldn't change anything. But it seems that I have an issue with the CMake version of the library build. I think there might be an error in here somewhere, but I don't think this is it. I'm inclined to back out this change. If you have more information which explain what I'm missing here, I'd be pleased to look at it.  
  
I dont need wide char archive, but was unable to compile serialization with BOOST_NO_STD_LOCALE enabled.  
  
There are four possible usage ways:  
1) non-widechar archive with locale  
2) widechar archive with locale  
3) non-widechar archive without locale  
4) widechar archive without locale  
  
The error directive trys to disable case 4, but disables also case 3. This change enables to compile serialization for case 3.  
  
>There is a much simpler way to disable wide character builds in this situation: Boost.Config  
  
Maybe, my knowledge of boost build system is small.

---
