# #17 Update result_of.hpp [Closed]

> Username: jzmaddock  
> Created at: 2014-07-04 15:49:16 UTC  
> Updated at: 2014-07-05 17:11:56 UTC  
> Closed at: 2014-07-05 17:11:56 UTC  
> Url: https://github.com/boostorg/utility/pull/17  

Allow Boost.Build to trace header dependencies, otherwise a full build from the boost root fails as the needed headers do not get installed.  The fix should be applied to master as well as develop of course.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-07-04 18:02:07 UTC  
> Url: https://github.com/boostorg/utility/pull/17#issuecomment-48063569  

I'm not sure why is this needed. 'b2 headers' should create all necessary links and as far as I understand, issuing this command is a requirement, isn't it?  
  
There are lots of other places in Boost that use preprocessor magic to include headers, and I'm not sure we want to patch all these places this way. There has to be a common solution. Right now it's 'b2 headers'. If Boost.Build wants to create links on the fly based on header dependencies then it has to learn to parse headers properly, IMHO.

---

## Comment 2

> Username: ericniebler  
> Created_at: 2014-07-04 18:23:14 UTC  
> Url: https://github.com/boostorg/utility/pull/17#issuecomment-48064382  

We should probably take this patch for the upcoming release, and if there are larger issues, to think about them for the next release.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-07-05 08:15:59 UTC  
> Url: https://github.com/boostorg/utility/pull/17#issuecomment-48080994  

For sure "bjam headers" fixes this, but:  
  
1) Will existing users realize this when downloading and building the next release?  My guess is they'll just do what they've always done and then wonder why the build is broken.  
2) It's my assumption (possibly incorrect) that in the brave new modular world a user should be able to do a "bjam --with-somelib" and have _only_ "somelib" and it's dependencies installed.  Aside: if "bjam headers" is to be a prerequisite then it should be an implicit not explicit target in the root Jamfile, and the library builds should declare it a dependency.  
  
Of course a better fix would be to have Boost.Build trace dependencies through BOOST_PP_ITERATE...  
  
Just my 2c...

---

## Comment 4

> Username: Lastique  
> Created_at: 2014-07-05 09:38:39 UTC  
> Url: https://github.com/boostorg/utility/pull/17#issuecomment-48082333  

1. My understanding is that the distributed archives should already include the boost/ directory with all headers. 1.56 should be indistinguishable from 1.55 in this respect. If not then the build procedure should take care of that (i.e. execute 'b2 headers' as a part of libraries build process).  
2. I agree that the long term goal for modular Boost is to be able to download and install only part of Boost. But we have to admit that 1.56 is not a modular release. There is a lot to do left in this direction, one thing being is the tool for proper dependency tracking through headers. You may have seen the discussions of this tool on the ML.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2014-07-05 11:01:21 UTC  
> Url: https://github.com/boostorg/utility/pull/17#issuecomment-48083717  

If 1.56 is to ship with a complete /boost/ subdirectory then I agree this is a non issue.  
  
BTW I asked about this on the ML: http://lists.boost.org/Archives/boost/2014/07/214938.php but no replies regarding this issue so far.

---

## Comment 6

> Username: mclow  
> Created_at: 2014-07-05 15:14:54 UTC  
> Url: https://github.com/boostorg/utility/pull/17#issuecomment-48088723  

We discussed this at C++Now, and there was quite a strong consensus that 1.56 should ship with the same file layout as previous releases. I have been building scripts to ensure this.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2014-07-05 17:11:56 UTC  
> Url: https://github.com/boostorg/utility/pull/17#issuecomment-48091436  

OK in that case I withdraw the fix.

---
