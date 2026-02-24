# #190 Enable hidden visibility by default [Merged]

> Username: Lastique  
> Created at: 2018-08-19 19:45:52 UTC  
> Updated at: 2018-10-31 08:52:37 UTC  
> Merged at: 2018-09-10 17:44:09 UTC  
> Closed at: 2018-09-10 17:44:09 UTC  
> Url: https://github.com/boostorg/boost/pull/190  

This commit uses the new visibility feature added in https://github.com/boostorg/build/commit/898ddfa1b6888424eb292942d1014261ac7b6183 and enables hidden visibility by default for Boost libraries.  
  
The discussion that led to this PR is here:  
  
http://boost.2283326.n4.nabble.com/all-Request-for-out-of-the-box-visibility-support-tt4704134.html

---

## Comment 1

> Username: apolukhin  
> Created_at: 2018-08-20 07:03:31 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-414217072  

I see CI failures in Boost.Regex. Could be related to https://github.com/boostorg/regex/issues/49 and https://bugs.launchpad.net/ubuntu/+source/llvm-toolchain-3.8/+bug/1664321

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-08-20 10:56:01 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-414277209  

I can only suggest adding `<visibility>global` to Boost.Regex jamfile right now.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2018-08-22 06:28:52 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-414925918  

To speed up the fixes I sent emails directly to the Boost.Regex and Boost.Locale maintainers.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2018-08-25 17:18:33 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-415984003  

Seems that the issues were resolved in submodules. I'm in favour of merging this PR.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2018-08-31 17:46:42 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-417740766  

@Lastique could you please rebase the PR on top of the boostorg/boost to force the CI  run on fixed submodules?

---

## Comment 6

> Username: glenfe  
> Created_at: 2018-09-10 17:44:14 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-419999436  

Looks like there was sufficient consensus (or insufficient dissent). We observe the effect on the develop testers.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2018-09-12 21:01:10 UTC  
> Updated_at: 2018-09-12 21:06:03 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-420796228  

I have test issues in boost.test example folder, which are not run by the current boost regression test (develop was green last week).   
What should I do?  
  
**Edit**: I cannot reproduce the error locally (XCode9.3). The build machines are on OSX10.10 and build with clang6 using `toolset=clang cxxflags="-stdlib=libc++ -std=c++11" linkflags="-stdlib=libc++"`

---

## Comment 8

> Username: swatanabe  
> Created_at: 2018-09-12 21:08:37 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-420798432  

AMDG  
  
On 09/12/2018 03:01 PM, Raffi Enficiaud wrote:  
> I have test issues in boost.test example folder, which are not run by the current boost regression test (develop was green last week).   
> What should I do?  
>   
  
a) How is that related to this issue?  
b) What kind of issues are we talking about?  
c) Do you think that these examples should  
   be run by the regression tests?  
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: raffienficiaud  
> Created_at: 2018-09-12 21:32:03 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-420805059  

a) rev 638c880c5a5896bfabc13cedc7cf83d7b65a2ba5 (master) was green on boost.test side, and it turned red without any change for GCC5.4 and clang6.  
  
b) it is firing an test error with shared linked boost.test. The test is short:  
  
        #define BOOST_TEST_MODULE Unit_test_example_08  
        #include <boost/test/unit_test.hpp>  
  
        BOOST_AUTO_TEST_CASE_EXPECTED_FAILURES( my_test1, 1 )  
        BOOST_AUTO_TEST_CASE( my_test1 )  
        {  
            BOOST_TEST( 2 == 1 );  
        }  
  
  but the decorator is not taken into account. `BOOST_AUTO_TEST_CASE_EXPECTED_FAILURES` creates a static object in a translation unit. I suspect that it gets removed. Since I am not able to reproduce from a more recent compiler, I also tend to think this is a compiler bug.  
  
c) absolutely! Those are valid use case. I am running those on my own CI. Do you think we can integrate those to the boost CI?

---

## Comment 10

> Username: swatanabe  
> Created_at: 2018-09-12 21:50:15 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-420809735  

AMDG  
  
On 09/12/2018 03:32 PM, Raffi Enficiaud wrote:  
> a) rev 638c880c5a5896bfabc13cedc7cf83d7b65a2ba5 (master) was green on boost.test side, and it turned red without any change for GCC5.4 and clang6.  
>   
> <snip> Since I am not able to reproduce from a more recent compiler, I also tend to think this is a compiler bug.  
>   
  
  Well, I don't have either of those compiler versions  
set up either, and I'm not really an expert on  
visibility issues, so I'll leave this for someone else.  
  
> c) absolutely! Those are valid use case. I am running those on my own CI. Do you think we can integrate those to the boost CI?  
>   
  
  The test Jamfile should explicitly build the examples, then.  
See https://www.boost.org/development/requirements.html#Integration  
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: apolukhin  
> Created_at: 2018-09-13 10:25:25 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-420959303  

My assumption is that compiler get nuts because of the `const&` usage https://github.com/boostorg/test/blob/4df5b095eaa967f5e2687978bd3d9467b758f28e/include/boost/test/unit_test_suite.hpp#L324  
  
I'll hopefully get to the PC in weekends to find out the fix for the issue

---

## Comment 12

> Username: raffienficiaud  
> Created_at: 2018-09-13 18:34:47 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-421108248  

@apolukhin I also think that the compiler removes the symbol erroneously. However, I am also wondering: this should be client code only? I mean, apart from that, boost.test seem to be working fine, means that the generated .so has all the necessary.  
  
What is your opinion on that?

---

## Comment 13

> Username: raffienficiaud  
> Created_at: 2018-09-16 16:45:46 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-421794120  

@apolukhin Did you have the chance to look deeper into this? As a workaround, how can I remove this option from boost.test?

---

## Comment 14

> Username: Lastique  
> Created_at: 2018-09-16 20:56:33 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-421832065  

@raffienficiaud You can add `<visibility>global` to the project requirements to force the default visibility, as was before.  
  
However, it might be better to mark the global variable with `BOOST_SYMBOL_VISIBLE` or `__attribute__((used))`, depending on which effect is intended. I have clang 6.0 but I'm not sure when I will have a chance to take a closer look at the code.  
  
We should probably move the discussion to a Boost.Test ticket.

---

## Comment 15

> Username: raffienficiaud  
> Created_at: 2018-09-17 05:17:46 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-421892191  

@Lastique thanks, I will check with the global or explicit visibility of those symbols, even if this seems to be a compiler bug.   
  
Concerning the move of this discussion to boost.test, I am not sure the issue will get the attention from your side if we do so.

---

## Comment 16

> Username: apolukhin  
> Created_at: 2018-09-17 08:33:12 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-421927505  

+1 for trying out `BOOST_SYMBOL_VISIBLE` or `__attribute__((used))`.  
  
P.S.: It took unexpectedly long to fix the visibility related test failures in Stacktrace library. I've finished a few hours ago and I had no chance to look at the issue with Test. I'll try hard to find some time at this week.

---

## Comment 17

> Username: Lastique  
> Created_at: 2018-09-17 09:54:10 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-421949945  

> Concerning the move of this discussion to boost.test, I am not sure the issue will get the attention from your side if we do so.  
  
You can mention me in that ticket if you want to draw my attention.

---

## Comment 18

> Username: apolukhin  
> Created_at: 2018-09-17 21:35:40 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422179840  

@raffienficiaud I've reproduced the issue locally and now trying to find a fix

---

## Comment 19

> Username: raffienficiaud  
> Created_at: 2018-09-17 21:45:16 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422182356  

@apolukhin Thanks for looking into this!

---

## Comment 20

> Username: apolukhin  
> Created_at: 2018-09-17 22:52:26 UTC  
> Updated_at: 2018-09-17 23:05:42 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422197454  

@raffienficiaud   
Found it out!  
  
Perpending all the `m_tu_decorators` in https://github.com/boostorg/test/blob/638c880c5a5896bfabc13cedc7cf83d7b65a2ba5/include/boost/test/impl/decorator.ipp with `singleton<collector>::instance().` fixes the problem.  
  
Most important functions are `collector::operator*( base const& d )` and `collector::store_in( test_unit& tu )`.  
  
I'm not sure that my fix fits the Boost.Test development practice (and I do not fully understand the Boost.Test internals). Please take a look that it's the right thing to put `singleton<collector>::instance().m_tu_decorators` all around the place. You should probably also check other `singleton<>` usages.

---

## Comment 21

> Username: raffienficiaud  
> Created_at: 2018-09-18 09:31:27 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422324070  

@apolukhin Thanks for digging this out! Do you have an explanation for this? The singleton itself has normally public visibility... I do not understand :)

---

## Comment 22

> Username: apolukhin  
> Created_at: 2018-09-18 19:30:54 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422519586  

I've made a PR for fixing some of the visibility issues: https://github.com/boostorg/test/pull/166  
  
BUT it does not work right now, but works if add `visibility=global` to the b2 run (without PR `visibility=global` does not help). Here's some creepy thing, as I understand it:  
  
-fvisibility=hidden works as expected when building shared objects. But does a strange thing for executables. It makes all the symbols hidden (probably because exporting symbols from the binary requires -rdynamic)  
  
So the full fix will require setting `visibility=global` for tests of the Boost.Test OR the creepy `singleton<collector>::instance().` thing from above. @raffienficiaud what would you prefer?

---

## Comment 23

> Username: raffienficiaud  
> Created_at: 2018-09-18 19:44:34 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422524869  

@apolukhin I've seen it and my CI is testing right now, many thanks. Apart from the visibility (symbol not being visible), I do not understand why the side effects of this symbol are not handled. It looks like, the symbols being removed, all their possible effects are also removed. This looks like a huge compiler bug to me, so I tend to think that we are missing something...

---

## Comment 24

> Username: apolukhin  
> Created_at: 2018-09-19 08:11:13 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422702248  

The side effects are not removed. They are applied to another singleton, that is not used after applying the stuff. I've put a stacktrace printing and it showed two singletons, one in the executable, the other one in the shared library of Boost.Test.

---

## Comment 25

> Username: raffienficiaud  
> Created_at: 2018-09-19 19:34:30 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422930959  

That explains - almost - everything, indeed! many thanks!   
  
But this also opens a new question, and the fact that Visual does the job right and not GCC or Clang is troubling.  
  
The singleton is marked with import/export symbol (through `BOOST_TEST_DECL`). It involves two classes `collector` and this template `singleton<collector>`. On the client code, there is a ref to the collector class, which does not call any constructs. This ref is taken by calling `collector& base::operator*`. In `base::operator*`, we call `collector::instance()`, so it should be calling the singleton made visible by `BOOST_TEST_DECL`.  
  
Once I am inside any `collector` member function, I should be inside the singleton. `singleton<collector>::instance()` is not explaining why I have 2 singletons. The client code itself should not have the code for generating a ctor for this.  
  
Any other decorator either refers directly to the singleton `collector::instance()` or to the `collector base::operator*` that calls `collector::instance()`.   
  
So the question remains open :-)

---

## Comment 26

> Username: raffienficiaud  
> Created_at: 2018-09-19 19:53:02 UTC  
> Updated_at: 2018-09-19 19:53:28 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-422936278  

From this https://gcc.gnu.org/onlinedocs/gcc-4.7.1/gcc/Function-Attributes.html  
  
* **visibility: default**:   
    * On ELF, default visibility means that the declaration is visible to other modules and, in shared libraries, means that the declared entity may be overridden.   
    * On Darwin, default visibility means that the declaration is visible to other modules.   
  
* **visibility: protected**: Protected visibility is like default visibility except that it indicates that references within the defining module will bind to the definition in that module. That is, the declared entity cannot be overridden by another module.   
  
Would protected would be better for a singleton?

---

## Comment 27

> Username: apolukhin  
> Created_at: 2018-09-21 16:58:14 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-423604656  

Nope, still bad. You'll apply the problem with executables to the shared objects. Here's how it would probably work: you have a.so and b.so, both have singleton instances. With default visibility linker will map both entities into one address (will make one entity). With protected visibility c.so will see singleton from a.so or b.so, but a.so and b.so will have their own instances of singletons. a.so will work with its own singleton, b.so will work with its own singleton, c.so will work with some singleton (from a.so or b.so)  
  
With executables the problem is that default visibility stops working on executables biuld with hidden visibility and without -rdynamic like flags. For that case executable a.out has it's own singleton, a.so has it's own singleton. Linker does not unify those, so a.so uses it's own singleton, while a.out uses its own.  
  
You may also try to hide the singleton entirely in the .so so that in the the executable it is never instantiated

---

## Comment 28

> Username: apolukhin  
> Created_at: 2018-09-22 18:40:50 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-423764668  

@raffienficiaud I've updated the PR https://github.com/boostorg/test/pull/166  
Now it works with any visibility flags (makes sure that singleton is instantiated only in ipp files).  
  
P.S.: have not test it on Windows

---

## Comment 29

> Username: raffienficiaud  
> Created_at: 2018-09-22 19:08:12 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-423766391  

@apolukhin Yes, I also fixed it like this, many thanks! but here are my findings:  
  
* visibility hidden != import/export: the DLL/MSVC mechanism as I understand it does not make a symbol "invisible". It instructs the linker on the location of the symbol, but this symbol is still visible. OTOH, the 'visibility hidden' hides completely the symbols that are coming from external translation units to the linker. The linker is free to generate one if needed, it has no means to know if the symbol exists in the global binary that is being generated.   
* By inspecting the symbols with `nm`, the only way to avoid the creation of the symbol on the client side was to hide its implementation in a `.cpp/.ipp` file. Means that the inlining of the `static` accessor to the symbol was not inheriting the visibility as we expected it. Even if I move the static member function to the derived class, I had the symbol duplication. This was in fact quite surprising.  
  
I started getting rid of the singleton implementation, as it does not what it is supposed to do properly.   
  
Thank you for the time you took for assisting me with this. I am still a bit worried as boost.test contains several singletons in different places. I will inspect with `nm`.

---

## Comment 30

> Username: Lastique  
> Created_at: 2018-09-22 20:39:18 UTC  
> Updated_at: 2018-10-31 08:52:37 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-423771748  

> the DLL/MSVC mechanism as I understand it does not make a symbol "invisible". It instructs the linker on the location of the symbol, but this symbol is still visible. OTOH, the 'visibility hidden' hides completely the symbols that are coming from external translation units to the linker. The linker is free to generate one if needed, it has no means to know if the symbol exists in the global binary that is being generated.  
  
`dllexport`/`dllimport` are indeed different from visibility, but not in the aspect you describe. By marking a symbol `dllexport` you instruct the compiler to generate some additional shims in the binary (which start with `__imp_` prefix) and the linker to emit this symbol in the export table and add the shims to the interface library (*.lib). `dllimport` does the opposite - it tells the compiler to rely on that symbol being present elsewhere with the `__imp_` prefix (and not define the symbol), and since the linker pulls the interface .lib library that symbol gets resolved and associated with the corresponding .dll library. Also, the linker generates the import table, which is used to resolve the symbols against the export tables of the linked .dlls at load time. So, in hindsight, `dllexport`/`dllimport` is mostly about mangling the symbols differently and putting them into export/import tables in the executable.  
  
Visibility is different in that it is simply a markup that can be applied to any symbol that is put in the symbol table of the module. It does not alter its mangling or causes additional code generation but it does get reflected in the symbol table of the shared library. The symbol table contains both the symbols that are implemented by the module and the symbols it is missing, and the runtime linker will try to resolve the missing symbols as it loads shared libraries into the running process.  
  
When you mark a symbol visible (i.e. with default or global visibility), the compiler will still emit it in every library where it's defined. But at load time only a single copy will be used (from the first library that gets loaded in the process). From the language perspective, this has pretty much the same effect as `dllexport`/`dllimport`. When a symbol is marked hidden, it is still emitted in the shared library and is also present in the symbol table of the library. But the compiler knows that the symbol cannot be referenced from outside the library, so it can apply additional optimizations, such as more efficient addressing modes. Or the compiler can simply remove the symbol if it knows it is not used in the library and such a change doesn't remove any side effects like a non-trivial constructor/destructor call. Normally, this should only be possible with LTO. Note that hidden visibility has nothing to do with visibility across *translation units* - a hidden symbol is still very much accessible across all translation units that comprise the module. Multiple definitions of a symbol with hidden visibility but [external linkage](https://en.cppreference.com/w/cpp/language/language_linkage) will still cause linker errors about duplicate definitions.  
  
Protected visibility is a rare beast, as it is not supported on all platforms. It works as the global (default) visibility, except that if the symbol is referenced from the same executable, the reference is always resolved to the copy in that executable. I'm not entirely sure how that works in cases when multiple shared objects in a process have the same symbol; most likely this is just a recipe for trouble, so just avoid it unless you know what you're doing.

---

## Comment 31

> Username: apolukhin  
> Created_at: 2018-10-20 17:56:14 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-431604402  

@glenfe how about merging this commit to master? Looks like all the issues were resolved and we still have time to stabilize master if something goes wrong.

---

## Comment 32

> Username: raffienficiaud  
> Created_at: 2018-10-21 14:48:11 UTC  
> Url: https://github.com/boostorg/boost/pull/190#issuecomment-431675032  

@apolukhin @glenfe Maybe an announce on the ML would also be good.

---
