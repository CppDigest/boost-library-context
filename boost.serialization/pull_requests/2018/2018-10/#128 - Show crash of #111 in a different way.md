# #128 Show crash of #111 in a different way [Closed]

> Username: Flamefire  
> Created at: 2018-10-16 16:42:43 UTC  
> Updated at: 2024-12-09 10:29:17 UTC  
> Closed at: 2018-11-17 19:30:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/128  

As @robertramey mentioned that the use of `extended_type_info` in #111 is invalid (can I have the related documentation for this?) I changed the example to be based on the official docu.  
  
Base is the basic serialization in the tutorial: https://www.boost.org/doc/libs/1_67_0/libs/serialization/doc/tutorial.html  
Additionally I want a specific level as described here: https://www.boost.org/doc/libs/1_67_0/libs/serialization/doc/traits.html#level  
  
Hence this code should now show the valid use of 2 shared libraries (possibly from different vendors and completely unrelated) each using static Boost.Serialization in a straight forward way.  
  
But it will show the exact same crash as in #111 due to the destruction order mixup with shared libraries on linux.

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-10-26 01:24:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-433256124  

I did run this test.  it ran with error on my machine.  I'm running OSX clang compiler version 10.  
  
...patience...  
...found 2341 targets...  
...updating 54 targets...  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/test_multi_shared_lib.test/clang-darwin-14/debug/link-static/visibility-hidden/test_multi_shared_lib.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/extended_type_info.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/extended_type_info_typeid.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/clang-darwin-14/debug/visibility-hidden/multi_shared1.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/binary_oarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/extended_type_info_no_rtti.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/binary_iarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/polymorphic_iarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/polymorphic_oarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/text_iarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/text_oarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/void_cast.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/xml_oarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/clang-darwin-14/debug/visibility-hidden/multi_shared2.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/test_multi_shared_lib.test/clang-darwin-14/release/link-static/visibility-hidden/test_multi_shared_lib.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/xml_iarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/clang-darwin-14/release/visibility-hidden/multi_shared1.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/binary_iarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/xml_grammar.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/binary_oarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/extended_type_info.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/extended_type_info_no_rtti.o  
clang-darwin.archive ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/libboost_serialization.a  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib: file: ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/libboost_serialization.a(stl_port.o) has no symbols  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib: file: ../../../bin.v2/libs/serialization/build/clang-darwin-14/debug/link-static/visibility-hidden/libboost_serialization.a(stl_port.o) has no symbols  
clang-darwin.link.dll ../../../bin.v2/libs/serialization/test/clang-darwin-14/debug/visibility-hidden/libmulti_shared_1.dylib  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/extended_type_info_typeid.o  
clang-darwin.link.dll ../../../bin.v2/libs/serialization/test/clang-darwin-14/debug/visibility-hidden/libmulti_shared_2.dylib  
clang-darwin.link ../../../bin.v2/libs/serialization/test/test_multi_shared_lib.test/clang-darwin-14/debug/link-static/visibility-hidden/test_multi_shared_lib  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_multi_shared_lib.test/clang-darwin-14/debug/link-static/visibility-hidden/test_multi_shared_lib.run  
**passed** ../../../bin.v2/libs/serialization/test/test_multi_shared_lib.test/clang-darwin-14/debug/link-static/visibility-hidden/test_multi_shared_lib.test  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/polymorphic_iarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/polymorphic_oarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/void_cast.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/text_iarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/text_oarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/test/clang-darwin-14/release/visibility-hidden/multi_shared2.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/xml_oarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/xml_iarchive.o  
clang-darwin.compile.c++ ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/xml_grammar.o  
clang-darwin.archive ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/libboost_serialization.a  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib: file: ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/libboost_serialization.a(stl_port.o) has no symbols  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib: file: ../../../bin.v2/libs/serialization/build/clang-darwin-14/release/link-static/visibility-hidden/libboost_serialization.a(stl_port.o) has no symbols  
clang-darwin.link.dll ../../../bin.v2/libs/serialization/test/clang-darwin-14/release/visibility-hidden/libmulti_shared_1.dylib  
clang-darwin.link.dll ../../../bin.v2/libs/serialization/test/clang-darwin-14/release/visibility-hidden/libmulti_shared_2.dylib  
clang-darwin.link ../../../bin.v2/libs/serialization/test/test_multi_shared_lib.test/clang-darwin-14/release/link-static/visibility-hidden/test_multi_shared_lib  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_multi_shared_lib.test/clang-darwin-14/release/link-static/visibility-hidden/test_multi_shared_lib.run  
**passed** ../../../bin.v2/libs/serialization/test/test_multi_shared_lib.test/clang-darwin-14/release/link-static/visibility-hidden/test_multi_shared_lib.test  
...updated 54 targets...  
$

---

## Comment 2

> Username: robertramey  
> Created_at: 2018-10-26 01:27:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-433256586  

Helpful tip:    
a) One need not run the whole test suite.  It's much, much faster to run the test one is interested in.  
b) It's much more convenient to run the test on one's local machine using b2.  Here's the command line I use:  
  
$ b2  toolset=clang-14 variant=debug,release link=static test_multi_shared_lib  
boost_root: /Users/robertramey/WorkingProjects/modular-boost  
locate_root: /Users/robertramey/WorkingProjects/modular-boost  
no errors detected  
$cat b2.log  
...

---

## Comment 3

> Username: Flamefire  
> Created_at: 2018-10-26 06:27:53 UTC  
> Updated_at: 2018-10-26 14:49:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-433300799  

> I did run this test. it ran with error on my machine. I'm running OSX clang compiler version 10.  
  
Did you meant to write **without** error?     
If so, then this is no surprise. It is the same result as travis (fails on linux, static, [GCC](https://travis-ci.org/boostorg/serialization/jobs/442270490), [Clang](https://travis-ci.org/boostorg/serialization/jobs/442270492)) and is mentioned in the PR description:  
  
> But it will show the exact same crash as in #111 due to the destruction order mixup with shared libraries on **linux**.  
  
Again this is due to the **implementation defined** behaviour of shared libraries and hence it is completely possible that results differ across OSs

---

## Comment 4

> Username: robertramey  
> Created_at: 2018-10-26 14:45:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-433432658  

Right - I meant without error

---

## Comment 5

> Username: Flamefire  
> Created_at: 2018-11-07 11:35:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-436595350  

Rebased on current develop. As explained on ML it still crashes like before:  
  
> *** Error in `bin.v2/libs/serialization/test/test_multi_shared_lib.test/gcc-4.8/debug/link-static/visibility-hidden/test_multi_shared_lib': double free or corruption (fasttop):

---

## Comment 6

> Username: robertramey  
> Created_at: 2018-11-17 19:30:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-439641682  

I believe this is resolved.  If anyone is unhappy about this, please open a new PR.

---

## Comment 7

> Username: Flamefire  
> Created_at: 2018-11-18 09:47:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-439680103  

@robertramey Neither this nor #111 was included in the current source as far as I could see. I think this test should still be included as it shows the implementation defined behaviour and why the runtime checks instead of asserts are required. Which one would you like? Any changes required?

---

## Comment 8

> Username: robertramey  
> Created_at: 2018-11-18 16:48:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-439707092  

I had that test included earlier just to see what it would do to the test matrix.  It passed on every platform tested.  I removed it for a couple of reasons.    
  
a) It didn't fail anywhere so I doubted it's validity and utility.  I wasn't convinced that it actually isolated a problem.  That is, if there's a problem somewhere, I'd like to see a test isolate it.  I think this test needs more work to do what it purports to do.  
  
b) It wasn't to the standard you've set by test_singleton_inherited, etc as far as comments, justification, explanation, copyright etc. A test added to the library becomes part of the library almost forever. From an esthetic viewpoint, I believe that it's valuable that we try to maintain a level of consistency. One could make the case that it's just syntactic sugar and hence irrelevant.  But I think it's helpful to persons in the future to be able to understand the library and how to fix it if necessary.  
  
Soooo - I don't object in principle object to including a test like this.  But I think this one needs more work.  
  
This whole episode does make the idea of separating the singleton into a separate boost library.  I've included features - basically the connected of "locked" and "is_destroyed" to support what the serialization library needs.  Separating this component into a separate library would result in a very interesting discussion as to what such a library should  contain.  I added in these features because I'm paranoid and trust no one.  But maybe that's just me.  Then there is the question of forcing initialization to occur before main.  And the question as to the way I handled the forcing of correct sequencing of dependent static objects.  
  
Making singleton a separate library would make things simpler.  I believe that part of the confusion created in this thread is due to singleton and the serialization and extended type info, etc seeming to be coupled when in fact they are not.  Factoring things in composable components promotes correctness and simplifies testing and maintenance.

---

## Comment 9

> Username: Flamefire  
> Created_at: 2018-11-18 18:04:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/128#issuecomment-439712670  

> I had that test included earlier just to see what it would do to the test matrix. It passed on every platform tested  
  
With my fix of the singleton included this is expected. Did you test this **with** my fix or without? If without, did you try on linux? Do you have a link to that test (travis etc.)? Because this DID show the crash (see https://github.com/boostorg/serialization/pull/128#issuecomment-433300799)  
  
As the singleton fix also removed the asserts this can now not show any more failures.  
  
But: This test creates a very specific situation: Using static Boost.Serialization from 2 shared libraries which did crash before and there is no other test like this ATM. These 2 facts make it worth being included.  
  
I'll happily create more docu to point out what this tests and what it does not: It does not test a specific code path but rather how this library handles the implementation defined behaviour.  
  
> I believe that part of the confusion created in this thread is due to singleton and the serialization and extended type info, etc seeming to be coupled when in fact they are not.  
  
`extended type info` uses the singleton. And the way it was used was wrong: There is a case where this assert does not hold: https://github.com/boostorg/serialization/commit/f297d80d6ef55ac66525320d0477c17806aa57cd#diff-d9472ab68b7d3466cc64073e4d16d2ecL98  
  
So this is not really a test of `singleton` but from more of the library: The coupling of the different singletons used.  
  
To get constructive: What I would add/clarify in the test docu:  
  
- Intended to test the combination of static boost in multiple shared libraries  
- Reproduces a former bug (at least the triggered asserts) in the coupling of internal components  
- This bug came from different destruction order due to implementation defined behaviour of shared libs  
  
What else should be clarified/added?

---
