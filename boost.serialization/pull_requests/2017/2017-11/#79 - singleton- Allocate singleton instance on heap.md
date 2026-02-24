# #79 singleton: Allocate singleton instance on heap [Closed]

> Username: res2k  
> Created at: 2017-11-21 20:47:13 UTC  
> Updated at: 2018-11-18 21:20:48 UTC  
> Closed at: 2018-11-17 19:27:47 UTC  
> Url: https://github.com/boostorg/serialization/pull/79  

This avoids issues at process exit when the inner static singleton_wrapper  
happens to be cleaned up before the singleton<> instance.  
Makes Travis checks pass on Linux.

---

## Comment 1

> Username: masterleinad  
> Created_at: 2017-11-22 11:12:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-346319766  

This probably tries to address the same issue as #69. Do you have a (small) test case that fails without this patch?

---

## Comment 2

> Username: res2k  
> Created_at: 2017-11-22 11:39:44 UTC  
> Updated_at: 2017-11-22 11:45:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-346325994  

Compare https://travis-ci.org/boostorg/serialization/builds/305474813 and https://travis-ci.org/boostorg/serialization/builds/301650246:  
On develop the `test_dll_exported` test consistently crashes on Linux. I could also reproduce that locally (gcc 6.4.1 on Fedora 25).

---

## Comment 3

> Username: masterleinad  
> Created_at: 2017-11-22 11:42:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-346326678  

Yes, that looks convincing. :smiley:

---

## Comment 4

> Username: robertramey  
> Created_at: 2017-11-22 17:48:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-346425836  

This looks promising.  It would explain why the problem occurs on some environments an not in others. One thing that has slowed us down is that we've been using test_dll_exported  to test this because that is where the problem showed up.  Looking through the test suite, I see there is no actual test for the singleton.  Clearly this would be useful.  So if anyone has or can make one, I'd be interested in seeing it and likely incorporating such a test in the set of tests.  This would be particularly valuable as apparently some are using the singleton module outside the serialization library.  And the singleton has special considerations regarding visibility, inclusion and the like.

---

## Comment 5

> Username: vasild  
> Created_at: 2017-11-22 19:03:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-346445042  

I confirm that the patch in this pull request fixes the crash I describe in https://github.com/boostorg/serialization/pull/69:  
  
```  
$ cat bug.cc   
#include <boost/archive/binary_iarchive.hpp>  
#include <boost/serialization/vector.hpp>  
#include <sstream>  
#include <vector>  
  
void f()  
{  
  std::stringstream iss;  
  boost::archive::binary_iarchive ar(iss);  
  std::vector<int> out;  
  ar >> out;  
}  
  
int main(int argc, char* argv[])  
{  
  return 0;  
}  
```  
Without the patch:  
```  
$ ./bug  
Bus error (core dumped)  
```  
  
With the patch:  
  
```  
$ ./bug  
$ valgrind ./bug  
==34751== Memcheck, a memory error detector  
==34751== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.  
==34751== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info  
==34751== Command: ./bug  
==34751==   
==34751==   
==34751== HEAP SUMMARY:  
==34751==     in use at exit: 1,544 bytes in 16 blocks  
==34751==   total heap usage: 16 allocs, 0 frees, 1,544 bytes allocated  
==34751==   
==34751== LEAK SUMMARY:  
==34751==    definitely lost: 0 bytes in 0 blocks  
==34751==    indirectly lost: 0 bytes in 0 blocks  
==34751==      possibly lost: 0 bytes in 0 blocks  
==34751==    still reachable: 1,544 bytes in 16 blocks  
==34751==         suppressed: 0 bytes in 0 blocks  
==34751== Rerun with --leak-check=full to see details of leaked memory  
==34751==   
==34751== For counts of detected and suppressed errors, rerun with: -v  
==34751== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
$  
```  
Cheerz!

---

## Review 6 [Commented]

> Username: juanchopanza  
> Created_at: 2017-11-24 16:32:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/79#pullrequestreview-78955218  

📁 include/boost/serialization/singleton.hpp

```diff
 128 |         use(& m_instance);
 124 |-         return static_cast<T &>(t);
 129 |+         if (!t) t = new singleton_wrapper;
```

> Username: juanchopanza  
> Created_at: 2017-11-24 16:31:11 UTC  
> Updated_at: 2018-02-07 14:35:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#discussion_r153002396  

This construct does not look thread safe, whereas the original was (at least since C++11). Could this be an issue?

> Username: masterleinad  
> Created_at: 2017-11-24 20:01:43 UTC  
> Updated_at: 2018-02-07 14:35:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#discussion_r153022258  

Just doing ` static singleton_wrapper *t = new singleton_wrapper;` above should be fine, don't you agree?

> Username: robertramey  
> Created_at: 2017-11-24 22:07:34 UTC  
> Updated_at: 2018-02-07 14:35:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#discussion_r153029930  

Hmmm - as far as I'm concerned the original is/was correct.  But it seems that with some combinations of compiler/library ...?  the order of destruction of the static variable is not the reverse of the construction - as they should be.  There is also the question of why we get any kind of memory issue when the program ends. A big motivation for using a static variable was that it would be built into the program image and never touch the heap.  But apparently that was wishful thinking.  It would qualify this as a work around for unexpected/obscure/incorrect runtime behavior regarding invocation of destructor of static variables.  Of course I don't really know.  It could be a bug / undefined behavior which no one has seen yet.  Given all this uncertainty, I'm putting in the change given that it addresses the failure of one of the tests.  
  
On the other hand, I've since changed the test to make it more correct.  It's possible that it was an error in the test that no one sees and that now it's gone. If more information comes to light we can easily change it again.  This is not a persistent object.  
  
So, for now, we'll just keep on trucking ...

> Username: juanchopanza  
> Created_at: 2017-11-24 22:25:41 UTC  
> Updated_at: 2018-02-07 14:35:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#discussion_r153030737  

@masterleinad Yes I think that would fix the concurrency issue I had in mind.

> Username: res2k  
> Created_at: 2017-11-24 23:29:57 UTC  
> Updated_at: 2018-02-07 14:35:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#discussion_r153033205  

About the heap allocation:  
This could be avoided by declaring a byte array of singleton_wrapper size (and alignment) and using placement new.  
  
But you'd need `boost::call_once` to ensure thread safety.  
  
While I noticed the problematic order of destruction I have not investigated the reasons in detail, so I unfortunately can't say what exactly goes wrong. But maybe I get around to getting a better explanation.

> Username: bangerth  
> Created_at: 2017-11-25 02:34:54 UTC  
> Updated_at: 2018-02-07 14:35:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#discussion_r153036339  

I agree that doing the assignment right in the declaration of the `static` local variable solves the problem. If you don't want to do this, then you need to do double-checking to make things thread-safe, as shown here: https://en.wikipedia.org/wiki/Double-checked_locking

> Username: robertramey  
> Created_at: 2017-11-25 05:51:54 UTC  
> Updated_at: 2018-02-07 14:35:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#discussion_r153038656  

Actually, if any of you guys want to make a name for yourself, you could factor out the singleton into it's own boost library/component and the serialization library would just import it.  When I made it it seemed simple - no heap allocation, initialization before main is called.  Destruction after main is exited etc.  A no brainer.  But it might be that the compiler handling of static variables throws a monkey wrench into all this.


---

## Comment 7

> Username: masterleinad  
> Created_at: 2017-11-29 17:26:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-347934010  

It seems you also need to rebase.

---

## Comment 8

> Username: robertramey  
> Created_at: 2017-11-29 17:34:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-347936503  

Hmmm - I'm not convinced here.  
  
One of the main problems that this singleton attempts to address is the the notorious "static variable initialization sequence" issue.  The original scheme guaranteed that that items would be be initialized only when first used.  This change would break that I believe.    
  
The only reason we're using a raw pointer anyway is to work around an issue which presents itself only in some older compilers.  Given the complexity of dealing with this, I'd really prefer that someone - maybe you - make a boost singleton library - maybe based on this so that the serialization library could just include it.

---

## Comment 9

> Username: bangerth  
> Created_at: 2017-11-29 22:28:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348018198  

That seems like an undue burden on @masterleinad that might lead to the problem never getting fixed :-( I would hope that it's possible to commit a fix without creating a new boost library.  
  
I understand being cautious around these issues. But it's not like everything is alright right now. The situation is already broken, and we know it used to work before.

---

## Comment 10

> Username: robertramey  
> Created_at: 2017-11-30 00:04:35 UTC  
> Updated_at: 2017-11-30 00:08:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348038491  

Hmmm -I believe it was always broken before - it just showed up on one new test test_dll_exported and only on some combinations of compiler/library/OS. As far as I know, there are no current bugs in the serialization library.  
  
 A change like this has the potential to cause all sorts of havoc.  Has anyone actually ran all the library tests with this change?

---

## Comment 11

> Username: davydden  
> Created_at: 2017-11-30 05:54:07 UTC  
> Updated_at: 2017-11-30 07:32:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348089931  

@robertramey   
  
> Hmmm -I believe it was always broken before  
  
AFAIK it was working before, specifically 1.63 is ok, 1.65.0 is ok, but not 1.65.1, see this issue https://github.com/dealii/dealii/issues/5262

---

## Comment 12

> Username: juanchopanza  
> Created_at: 2017-11-30 06:01:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348091067  

Even though the issue is affecting me, I would also urge caution here. Furthermore, it isn't clear to me that the problem is fully understood. It would really help confidence if someone who understands it could provide a more detailed analysis. @robertramey BTW I am also curious as to the reasons for the current complex structure with a function-local static and a static data member. Is this the right forum to ask about that?

---

## Comment 13

> Username: vasild  
> Created_at: 2017-11-30 07:00:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348100256  

> Hmmm -I believe it was always broken before  
  
No, from the duplicate report at https://github.com/boostorg/serialization/pull/69:  
  
> I confirmed that the bug was introduced in b0a794d. There is no crash with b0a794da~ and a crash with b0a794d (which is from Feb 2017).

---

## Comment 14

> Username: robertramey  
> Created_at: 2017-11-30 15:47:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348228432  

I know the bug was detected in  b0a794d  But that does not convince me that that it was introduced in that version.  In other words, I think the bug is somewhere in the the standard library/compiler implementation and that the change in b0a794d only reveals what was only there.  Our "fix" is not really a fix but rather a work around some phenomenon that we don't understand.

---

## Comment 15

> Username: robertramey  
> Created_at: 2017-11-30 16:02:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348233528  

juanchopanza - the "complex" structure is the triumph of implementation over design.  That is, it wasn't actually "designed".  I started with something simpler but had to make it a little more complex in order to make it work.  A simpler design fails to address a number of issues:  
  
a) sequence of nested static objects.  static object a refers to static object b etc.  This means that b has to be created before a.  But this is under control of the compiler/library before "main" is called - or not.  
  
b) Compilers will optimize code by elimination of code not explicitly referred to.  The serialization library creates static objects to contain functions for de-serialization of exported derived pointers.  These pointers are accessed through the vtable of the abstract base class so the compiler never sees any reference to them.  So some method has to be sure that the compiler doesn't throw them away.  In some cases this problem will manifest itself as a failure to link an in others as a runtime error.  Note that behavior usually varies between debug and release modes, between compilers and compiler versions and whether the library is deployed as shared library/DLL or static library.  Also different tests show different problems.  It's not trivial to make this work across all these combinations.  If fact the number of combinations is so large, that it's not really possible to test them all.  And since we're relying on undefined behavior here, testing is actually the only way to make a guarantee that the library works.  
  
c) The above is a result of the design decision to permit deep serialization to objects through pointers.  This is a key feature of the Boost Serialization library.  There are libraries which don't do this - e.g. Cereal which as a consequence are much, much simpler.  In many cases, they might be a better choice.  But they are not comprehensive.  The blessing/curse of the Boost serialization library is that one can use it serialize virtually any C++ data structure without having to tamper with the structure design itself. That is, one can more or less completely de-couple the serialization from the rest of one's program.  This is very convenient for the library user, but comes at a heavy cost of complexity for the library developer/maintainer

---

## Comment 16

> Username: bangerth  
> Created_at: 2017-12-01 23:19:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348640050  

@robertramey: Pragmatically, do you have evidence that the current code is "more correct" than it was before? I do very much sympathize with your desire to come to a well-understood, well-designed code structure. But I'm not sure anyone has the gumption to actually make it happen, and it would be a shame if the perfect stood in the way of improvement.   
  
We know that the code used to work and broke with a particular commit (works just before that patch, fails just after it). Furthermore, this commit can be reverted without too much trouble IIRC. I'm not the expert in this area, but the commit message that went along with it ("Trying to get minGW to function for serialization library") at least doesn't explain what was wrong before and why the change made is correct. Do you recall why this particular change was made?

---

## Comment 17

> Username: robertramey  
> Created_at: 2017-12-02 00:33:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348650804  

The change was made to address failure of all tests with mingw.  These tests are all passing now.  I don't recall for a fact whether it was this change or some subsequent one which made the mingw tests pass again.

---

## Comment 18

> Username: bangerth  
> Created_at: 2017-12-02 00:56:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348653484  

So, moving forward, where do we find someone with a MingW system who could test the proposed patch to see what happens?

---

## Comment 19

> Username: robertramey  
> Created_at: 2017-12-02 01:26:49 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348656552  

LOL - actually, since I made this change I installed a mingw system just to be able to test this.  I don't remember whether I tested this specific change on it.  FYI I use a Mac with OSX.  I bought Parallels, installed Windows, ubuntu linux and Mingw.  It was basically the only way I could track down these kind of problems.  Short version - works great for these issues.  So I could investigate this some more.  But it still takes a lot of time because it likely ends in a game of whack-a-mole.  Fix it for mingw and it breaks mdvc, fix that and it breaks gcc, etc.

---

## Comment 20

> Username: davydden  
> Created_at: 2017-12-02 08:00:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-348676034  

meanwhile, @res2k could you please rebase to fix the conflict?

---

## Comment 21

> Username: bangerth  
> Created_at: 2017-12-19 18:22:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-352843622  

@robertramey -- since you seem to be the only one with a system on which this can be tested, would you mind testing the patch there?  
  
I don't know what else to suggest: I'm not a BOOST maintainer, so the decision is not mine, but if this was in the projects I work on, I'd choose reverting a small part of a patch that we know broke something, over waiting for the ultimate patch that is, in all likelihood never going to arrive :-(  
  
Short of that, do you have any suggestions how we can proceed with this?

---

## Comment 22

> Username: masterleinad  
> Created_at: 2017-12-19 23:14:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-352915744  

Just for clarification: The first commit of this PR was merged meanwhile (https://github.com/boostorg/serialization/commit/7d216b47619f6b7aa57c5f0e48b9131a025426f9) leaving the fix of the possible thread-safety issue in this PR.

---

## Comment 23

> Username: bangerth  
> Created_at: 2017-12-20 04:26:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-352961580  

Ah, thanks for the clarification and for rebasing what's left. The remainder appears to be a pretty straightforward change.

---

## Comment 24

> Username: jdmairs  
> Created_at: 2017-12-21 17:11:49 UTC  
> Updated_at: 2017-12-21 17:12:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353405471  

I too ran into a problem with 1.65.1 where my historic raw archives using boost serialization failed to import.  I'm using msvc VS2017 15.3 and 15.4.2.  Is there any easy way I can grab a whole file replacement to patch this problem?  I would drop back to boost 1.64 but I've already updated my msvc compiler to 15.3 and 1.64 does not recognize the compiler.  And to make things worse we use offline installers and no one has VS2017 15.2 anymore!  
  
I also just tried boost 1.66 and my problem still remains.

---

## Comment 25

> Username: robertramey  
> Created_at: 2017-12-21 17:26:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353408844  

@jdmairs "I too ran into a problem with 1.65.1 where my historic raw archives using boost serialization failed to import."  
  
a) "too" refers to some other instance?  Where is that?  
  
b) I can't comment on this with knowing the specific case.  It should never happen.  I'm careful to keep it from happening.  Alas it has still happened a couple of times.  When it does, the only cure has been to track down the specific case.  Usually it's traceable to something outside the library, but not always.

---

## Comment 26

> Username: davydden  
> Created_at: 2017-12-21 17:28:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353409386  

@jdmairs   
  
> Is there any easy way I can grab a whole file replacement to patch this problem?  
  
we have a patch in Spack [here](https://github.com/spack/spack/blob/develop/var/spack/repos/builtin/packages/dealii/boost_1.65.1_singleton.patch) which fixes all the issues with `1.65.1` for us.

---

## Comment 27

> Username: jdmairs  
> Created_at: 2017-12-21 18:47:50 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353427295  

@robertramey   
  
Sorry for not being precise.  "Too" means that I am having a showstopper issue when I upgraded from boost v1.64 to v1.65.  Exact same baseline...only thing that changed was the new boost.  I then assumed/hoped that anybody else having a similar experience (https://github.com/dealii/dealii/issues/5262) and my problem were the same.

---

## Comment 28

> Username: jdmairs  
> Created_at: 2017-12-21 18:50:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353427906  

@davydden   
  
I'm stuck on subversion and do not have very good git skills.  Without having git command line available is there a way I can use the browser to acquire a fully patched file and try it on my 1.66 baseline.  can you email me the full file?  Maybe this Christmas break I'll take that pluralsight course on Git.

---

## Comment 29

> Username: davydden  
> Created_at: 2017-12-21 19:09:50 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353432723  

@jdmairs this is a patch file, u don’t need Git to apply it. I am AFK but ‘patch’ command is enough, please google how to apply patches on Linux

---

## Comment 30

> Username: masterleinad  
> Created_at: 2017-12-21 19:53:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353442792  

@jdmairs The number of changes required is really not that large so you can just do it by hand.

---

## Comment 31

> Username: bangerth  
> Created_at: 2017-12-21 19:55:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353443278  

@jdmairs -- you are just hijacking an existing thread for your problem. Please open a new issue rather than adding something to a thread that has nothing to do with what we are discussing in the current thread.   
  
When you do open a new issue, you will want to be more specific than just saying "my historic raw archives using boost serialization failed to import". You'll have to describe what happens, concretely, and what you expect to happen.

---

## Comment 32

> Username: jdmairs  
> Created_at: 2017-12-22 16:47:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-353634847  

In case anyone stumbles onto my hijack I moved my issue to its proper place.  
  
[ptime no longer serializes between 1.64 and 1.65](https://github.com/boostorg/date_time/issues/56)  
  
Thanks for the folks that helped me out especially @masterleinad for making me realize that big bad patch file wasn't that scary.

---

## Comment 33

> Username: tobias-loew  
> Created_at: 2018-01-17 11:49:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-358281373  

I seems that the change creates memory leaks (https://github.com/boostorg/serialization/issues/86). Can anyone confirm this?

---

## Comment 34

> Username: gast128  
> Created_at: 2018-01-30 08:54:10 UTC  
> Updated_at: 2018-01-30 09:19:47 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-361519849  

The Boost.Serialization singleton is never cleaned up leading to memory leaks. Even more severely it seems to crash our application now if a DLL's is dynamically unloaded and loaded. We have to go back to a previous version. Again a Boost.Serialization issue which breaks our Boost transition.  
  
Btw we have our own bad experiences with singletons. Maybe an alternative is to drop them altogether and require that a serialized class would define extra information, e.g. on the place where the class is defined. This is more work from a user perspective but is probably preferable to these issues.

---

## Comment 35

> Username: tobias-loew  
> Created_at: 2018-01-31 09:20:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-361871821  

IMHO the changes to the singleton-class done in this thread should be reverted for the following reasons:  
  
- the initial solution of putting the internal instance on the heap seemed to work for the gcc guys but broke the singleton-pattern by demanding singleton-variables for correct heap clean-up (btw the memory leak from above  
==34751== LEAK SUMMARY:  
==34751==    definitely lost: 0 bytes in 0 blocks  
==34751==    indirectly lost: 0 bytes in 0 blocks  
==34751==      possibly lost: 0 bytes in 0 blocks  
==34751==    still reachable: 1,544 bytes in 16 blocks  
==34751==         suppressed: 0 bytes in 0 blocks  
==34751== Rerun with --leak-check=full to see details of leaked memory  
suggests that there weren't any singleton-variables tested)  
  
- the PR https://github.com/boostorg/serialization/pull/79/commits/9f6bc5057a57f902e439ebd815cf6d91379f73f8 still allocates the instance on the heap, but destruction-semantics are the same like using a static variable, thus the heap-allocation only produces overhead  
  
- the standard specifies that the dtors shall be sequence opposite to the completion-order of the ctors, thus the problem for the gcc-crash is some non-standard behaviour and should be fixed in gcc and not by spoiling singletons for all others (maybe someone could make a BOOST_GCC_WORKAROUND)

---

## Comment 36

> Username: res2k  
> Created_at: 2018-02-07 14:45:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-363790549  

I took another look at the issue.  
  
For one, I tried to understand the reason behind the destruction order issue. As a local static variable is initialized lazily, or possibly not at all, it's cleanup function will be registered dynamically as well (See eg https://godbolt.org/g/YkApUv - note the `_cxa_atexit` call). That dynamic registration appears to make some mistake - the cleanup function is associated with a shared library that's cleaned up earlier than the module the static variable actually "lives" in is cleaned up - with code executed later assuming the variable is still valid.  
That scenario can be worked around by using a global static var to handle the cleanup.  
  
However, _other_ ordering issues appeared with MSVC static builds. There the "solution" is to actually use `atexit()`…  
  
So, on the one hand, that all appears to work (at least the automated tests look promising, see here: https://travis-ci.org/res2k/serialization/builds/338525872 and here: https://ci.appveyor.com/project/res2k/serialization/build/1.0.31-develop). On the other, distinguishing the cases makes the implementation a bit messy. I'll probably try if I can find a cleaner yet one-size-fits-all implementation.

---

## Comment 37

> Username: Flamefire  
> Created_at: 2018-04-25 15:48:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384335884  

You are still making the mistake I described in #104: You have ctor/dtors for `singleton`, expected them to do something but never instantiate it. Hence the `destroyed` flag is never set! This causes crashes for certain destruction orders (e.g. `extended_type_info_typeid` singleton destructed after `ktmap` singleton (here: https://github.com/boostorg/serialization/blob/b73329fe45b02965c9f2059f7d90c285db1b3630/src/extended_type_info_typeid.cpp#L98)

---

## Comment 38

> Username: Flamefire  
> Created_at: 2018-04-25 16:13:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384344573  

@robertramey   
  
> I know the bug was detected in b0a794d But that does not convince me that that it was introduced in that version. In other words, I think the bug is somewhere in the the standard library/compiler implementation and that the change in b0a794d only reveals what was only there. Our "fix" is not really a fix but rather a work around some phenomenon that we don't understand.  
  
The bug was introduced in that revision. See #104 for proof.  
  
> The original scheme guaranteed that that items would be be initialized only when first used. This change would break that I believe.  
  
This is (currently and for a while) not true. The scheme used makes sure all singletons are created BEFORE `main` is entered (so one can use `lock` although I question its use)

---

## Comment 39

> Username: res2k  
> Created_at: 2018-04-25 16:39:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384352786  

Actually, is the `singleton<>` ctor even called?  
Anyway, I'd try dealing with `get_is_destroyed()` in `cleanup_func()` as well.

---

## Comment 40

> Username: Flamefire  
> Created_at: 2018-04-25 18:39:37 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384391684  

The `singleton` is never created, so no. That is my point

---

## Comment 41

> Username: res2k  
> Created_at: 2018-04-26 08:55:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384565347  

I did a quick test on the branch for this PR: I put `assert(false)` in `singleton` ctor resp. dtor, on gcc/Linux;  running the unit tests, these asserts generally fired.  
(Please take note this branch is not merged into mainline serialization, so issues there may not apply here, and vice versa.)

---

## Comment 42

> Username: Flamefire  
> Created_at: 2018-04-26 11:50:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384612510  

Then where is it constructed? I'll have another look too and contribute a test for the singleton itself

---

## Comment 43

> Username: Flamefire  
> Created_at: 2018-04-26 14:16:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384656842  

Ok found it. There is some trickery done there. Some classes are derived from `singleton<T>`. So  `singleton<T>` will instantiate `singleton_wrapper<T>` which is derived from `T` and hence from `singleton<T>` (ouch, my brain!)  
  
I think this is intended and should be the way `singleton<T>` has to be used as one can then simply use `T::get_const_instance()`. However for some classes this is not possible (hello `std::map`) which leads to problems as `singleton<T>::get_const_instance()` was not meant to be used. Maybe this should be enforced?

---

## Comment 44

> Username: robertramey  
> Created_at: 2018-04-26 15:45:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384690604  

OK - I'm going to take another look at this.  I wasn't happy with the original "fix", but things need to be fixed. So it looks like we'll have to fix this "fix". I hate this stuff.  If some want's to submit a test which detects a memory leak, that might be useful to cut the "whack a mole" cycle and keep us moving forward.

---

## Comment 45

> Username: alfC  
> Created_at: 2018-04-26 15:52:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384692697  

Hi, I am a user of serialization. Can anyone explain the Singleton issue in  
simple terms. Why a "Singleton" is needed? Is there some sort of global  
state of the library? Does it define an "environment"? Why would it leak  
memory? Should the user do something specific about it?  
  
On Thu, Apr 26, 2018, 08:45 Robert Ramey <notifications@github.com> wrote:  
  
> OK - I'm going to take another look at this. I wasn't happy with the  
> original "fix", but things need to be fixed. So it looks like we'll have to  
> fix this "fix". I hate this stuff. If some want's to submit a test which  
> detects a memory leak, that might be useful to cut the "whack a mole" cycle  
> and keep us moving forward.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/pull/79#issuecomment-384690604>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACMfYB-8S_OpctCnyzlSB_hkw1NWOG_Bks5tseu1gaJpZM4QmdrV>  
> .  
>

---

## Comment 46

> Username: Flamefire  
> Created_at: 2018-04-26 17:22:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384721424  

Every type used in serialization registers itself with the serialization library. This is actually 2 singletons: One for the registered type and one for the register itself. During destruction of the registered type it unregisters itself.  
  
The problem was, that the register was destructed before the registered type and so unregistering lead to a crash due to the destruction check not working. This was partially fixed by using a heap allocated singleton instance which "fixed" it by leaking the register singleton (and possibly some more)  
  
And no the user can't do much about this. Use Boost >= 1.66 and don't use serialization in a shared library that is loaded/unloaded multiple times as this will build up wasted memory.

---

## Comment 47

> Username: alfC  
> Created_at: 2018-04-26 17:45:17 UTC  
> Updated_at: 2018-04-26 18:11:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384728454  

On Thu, Apr 26, 2018 at 10:22 AM, Alexander Grund <notifications@github.com>  
wrote:  
  
> Every type used in serialization registers itself with the serialization  
> library. This is actually 2 singletons: One for the registered type and one  
> for the register itself. During destruction of the registered type it  
> unregisters itself.  
>  
Ok, probably is for the "tracking" feature. That sounds like an implicit  
environment (global state).  
(It reminds me of the registration of 'user derived data_types' in MPI;  
there is never a good place to register/commit them and produces insane managing  
issues. https://www.mpich.org/static/docs/v3.2/www3/MPI_Type_commit.html  .  
The only sane solution is that types can only be registered manually in the  
main() program because all hopes of automatic registration blow up memory.)  
  
Maybe this registration in Boost.Serialization can be made at the level of  
the Archive type? So when the archive is destroyed the registered types go  
away?  
That will introduce extra state for the Archive type but it is the only  
object with a definite lifetime.  
  
> The problem was, that the register was destructed before the registered  
> type and so unregistering lead to a crash due to the destruction check not  
> working. This was partially fixed by using a heap allocated singleton  
> instance which "fixed" it by leaking the register singleton (and possibly  
> some more)  
>  
Whatever that singleton was, perhaps it can be made part of the Archive  
type. (yes, creating custom Archive types will be more difficult from the  
user perspective). Having said that I must admit that when I create my own  
custom Archives, I never implement the tracking features because it is too  
complicated for my purposes. (I just don't serialize stuff through pointers  
automatically because that means that the object shouldn't be serialized "easily" in  
the first place, only "value types" should be serialized.)  
  
> And no the user can't do much about this. Use Boost >= 1.66 and don't use  
> serialization in a shared library that is loaded/unloaded multiple times as  
> this will build up wasted memory.  
>  
  
The loading multiple times affects me. Can the user avoid this problem by  
disabling tracking somehow?  
Can this be solved by making the library header-only?  
https://github.com/boostorg/serialization/issues/71  
  
Thank you for the explanation.

---

## Comment 48

> Username: Flamefire  
> Created_at: 2018-04-26 21:16:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-384791539  

Imo it cannot be avoided. The singleton gets instantiated for every type used (I think)  
  
No other solution possible than fixing it.   
  
I'm working on it too (look at my fork. I got test cases atm and will fix them in the next days)

---

## Comment 49

> Username: Flamefire  
> Created_at: 2018-04-27 15:51:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-385013159  

Ok I have my own branch ready. Waiting for the tests at https://ci.appveyor.com/project/Flamefire/serialization and https://travis-ci.org/Flamefire/serialization  
  
The branch is https://github.com/Flamefire/serialization/tree/feature/singleton-fix

---

## Comment 50

> Username: Flamefire  
> Created_at: 2018-05-07 15:34:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-387104618  

@alfC I added a PR which you can use to batch your version of boost to avoid the problem in the meantime. If you encounter any problems, tell me in that PR: #105

---

## Comment 51

> Username: robertramey  
> Created_at: 2018-11-17 19:27:47 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-439641473  

I believe this is now addressed.  If anyone is unhappy with the resolution, open a new PR

---

## Comment 52

> Username: bangerth  
> Created_at: 2018-11-18 19:12:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-439717492  

@robertramey: For completeness, could you say which pull request/commit fixed the issue? This way it's easier for others later on to follow the conclusion of the discussion.

---

## Comment 53

> Username: robertramey  
> Created_at: 2018-11-18 19:31:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-439718938  

sorry I can't do this.  the initial fix was just reversing the latest commit to fix another problem.  Once the this was done, the original problem reared it's ugly head generating a months long saga which only recently has started getting sorted out.  You can reconstruct all this yourself if it's important to you, but I personally can't spare the time to do this.

---

## Comment 54

> Username: Flamefire  
> Created_at: 2018-11-18 21:20:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/79#issuecomment-439726837  

@bangerth The commit was f297d80d6ef55ac66525320d0477c17806aa57cd which is mostly my PR #131   
This might not be 100% correct, but answers your question

---
