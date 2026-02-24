# #59 Fix regression (vs 1.66.0 and older) to allow argc == 0 for command_line parser [Closed]

> Username: b-spencer  
> Created at: 2018-07-25 20:14:02 UTC  
> Updated at: 2023-12-16 18:30:51 UTC  
> Closed at: 2023-12-16 18:30:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/59  

When upgrading from Boost 1.66.0 to 1.67.0, a test failed because `boost::program_options::command_line_parser` crashed when passed an `argc` value equal to 0.  While this isn't a value that a program is likely to get from `main()`, it seems reasonable that it should work to define an empty set of input arguments, especially since that has been the traditional (if undocumented) behaviour of this class.  
  
The commit that caused this change in behaviour seems to be https://github.com/boostorg/program_options/commit/110772c0ac672ef2972d251886215bb89933bf00.  
  
This PR fixes this regression and adds a test for such cases.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-25 20:29:04 UTC  
> Updated_at: 2018-07-25 21:52:09 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-407884828  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/59?src=pr&el=h1) Report  
> Merging [#59](https://codecov.io/gh/boostorg/program_options/pull/59?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/d95d31684832075fda04c9fc916f8ec875552763?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/59/graphs/tree.svg?height=150&width=650&token=UMdM0EgHK3&src=pr)](https://codecov.io/gh/boostorg/program_options/pull/59?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #59   +/-   ##  
========================================  
  Coverage    49.89%   49.89%             
========================================  
  Files           23       23             
  Lines         1385     1385             
  Branches       707      707             
========================================  
  Hits           691      691             
  Misses         110      110             
  Partials       584      584  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/59?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/program\_options/detail/parsers.hpp](https://codecov.io/gh/boostorg/program_options/pull/59/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZGV0YWlsL3BhcnNlcnMuaHBw) | `72.97% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/59?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/59?src=pr&el=footer). Last update [d95d316...b6e5a27](https://codecov.io/gh/boostorg/program_options/pull/59?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vprus  
> Created_at: 2018-08-02 06:22:45 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-409819403  

Thanks for the patch! Could you clarify the use case where it's necessary? The argv/argc version of the parse is specifically created so that you can pass arguments from main directly, in all other cases you can use the vector version. Is that somehow now suitable for you?

---

## Comment 3

> Username: b-spencer  
> Created_at: 2018-08-02 13:55:33 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-409934668  

I've found that the library is also useful for parsing arguments that are not directly received from `main()` itself.  So you may be dealing with arguments that you got from "other code" but happen to be in a form more convenient to pass to the C-style interface instead of the `vector<string>` interface.  
  
Given that the vector overload accepts an empty sequence, I couldn't think of a reason that this overload shouldn't also accept an empty sequence.    
  
I guess from my point of view, the question is "Why _not_ allow an empty sequence?  What advantage does that provide?"  To me, it seems that the disadvantage of prohibiting the empty sequence here is that it is not obvious, and possibly a subtle way to invoke undefined behaviour.

---

## Comment 4

> Username: xnox  
> Created_at: 2018-11-01 17:18:05 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-435115515  

I found an example in the real world of this usage. Currently mongodb fails to built from source, and has testsuite errors, if one builds with a system boost and said boost is 1.67 or newer.  
  
For details see https://jira.mongodb.org/browse/SERVER-37282  
  
Testsuite fails at options_parser_test.cpp at EmptySingleName test case, with this pull request applied, the test suite failures are resolved.  
  
I will be uploading this pull request change into Debian and Ubuntu boost1.67, but it would be nice if this is merged into upstream release of boostorg/program_options as this will affect mongodb eventually.

---

## Comment 5

> Username: vprus  
> Created_at: 2018-11-13 06:38:06 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438154467  

@xnox: I have looked at MongoDB code, at https://github.com/mongodb/mongo/blame/master/src/mongo/util/options_parser/options_parser_test.cpp and don't see where C-array of string is passed. Only vector is passed, and behavior for vectors was not changed. Am I overlooking something?

---

## Comment 6

> Username: xnox  
> Created_at: 2018-11-13 13:44:25 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438270298  

That's the test code that now fails, the code under test is this bit I think https://github.com/mongodb/mongo/blob/master/src/mongo/util/options_parser/options_parser.cpp#L1124

---

## Comment 7

> Username: xnox  
> Created_at: 2018-11-13 13:48:15 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438271536  

The failure is like so  
  
```  
[cpp_unit_test:options_parser_test] 2018-09-24T12:24:10.549+0000 	EmptySingleName	 std::exception: std::bad_alloc in test EmptySingleName  
```  
  
Which is unexpected. With this pull request applied, the testcase passes fine. So internals do rely on the previous behaviour.

---

## Comment 8

> Username: vprus  
> Created_at: 2018-11-13 20:50:11 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438431635  

Hmm, so while program_options has API that takes vector of strings, since  
the start, that code in Mongo converts vector of strings to argc/argv?  
Ideally, it should be fixed. Do you know if that is likely to happen?  
  
On Tue, 13 Nov 2018, 16:48 Dimitri John Ledkov, <notifications@github.com>  
wrote:  
  
> The failure is like so  
>  
> [cpp_unit_test:options_parser_test] 2018-09-24T12:24:10.549+0000 	EmptySingleName	 std::exception: std::bad_alloc in test EmptySingleName  
>  
> Which is unexpected. With this pull request applied, the testcase passes  
> fine. So internals do rely on the previous behaviour.  
>  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/pull/59#issuecomment-438271536>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANye_fk3epeYkw0MEPWGL-rEwY2aYYSks5uus2fgaJpZM4VgufS>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com

---

## Comment 9

> Username: b-spencer  
> Created_at: 2018-11-13 21:42:40 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438448865  

Why not take the PR and avoid `argc == 0` leading to undefined behaviour?  I don't see the downside.

---

## Comment 10

> Username: vprus  
> Created_at: 2018-11-14 06:17:16 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438550950  

The downside is that argv interface is inconsistent. If you pass argc of 0,  
it's empty sequence. If you pass argc of 1, it's also empty sequence. If  
you pass argc of 2, it's one element sequence. Besides main, such interface  
is not used anywhere, even in C  
  
On Wed, 14 Nov 2018, 00:42 Brad Spencer, <notifications@github.com> wrote:  
  
> Why not take the PR and avoid argc == 0 leading to undefined behaviour? I  
> don't see the downside.  
>  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/pull/59#issuecomment-438448865>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANyew7gcTD5faPA6eTms3o7DIY6lGWBks5uuzzQgaJpZM4VgufS>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com

---

## Comment 11

> Username: xnox  
> Created_at: 2018-11-14 08:43:58 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438582747  

@vprus I'm not mongo upstream... I am simply a downstream distribution (Ubuntu) trying to build both boost and mongo for everyone.... I did initially raise this as a bug in mongo..... https://jira.mongodb.org/browse/SERVER-37282  They are also on github, can you please explain and/or suggest fixes in that mongo issue as to how to fix this there in a more C++/boost-way such that it is compatible with both old and new boost using vectors.  
  
Undefined behavior resulting in std::bad_alloc being thrown is also not nice. at the very least, something easier to comprehend should be thrown instead? Or indeed just continue to allow this odd way of passing things, maybe with a large warning "you are doing things wrong" ?

---

## Comment 12

> Username: b-spencer  
> Created_at: 2018-11-14 14:15:00 UTC  
> Updated_at: 2018-11-14 14:16:31 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438675871  

I'll grant that `argc == 0` is weird.  But, what is "handling it gracefully" inconsistent _with_?  It's pretty common to pass `nullptr, 0` to pointer, size arguments to mean "empty sequence".  
  
And regardless, I just don't see the harm of handling "crazy input" as "zero arguments" instead of undefined behaviour.    
  
  
If we take this PR, who gets hurt?  If we don't take this PR, who gets hurt?

---

## Comment 13

> Username: xnox  
> Created_at: 2018-11-14 19:35:43 UTC  
> Updated_at: 2018-11-14 19:37:04 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-438790266  

I don't think there are any upside or winners.  
  
With or without this PR, zero options result in no options.  
  
Those that did cause such a thing, in the prior releases learned to trap / test that it is in fact treated as zero arguments (as in check that wicked input, doesn't result in any args being parsed). And for them to continue doing that they need this PR going forward to effectively revert to old behaviour.  
  
Without this PR they would need to learn, that yes the code is crazy, but from boost version XY this instead of returning no arguments, is now throwing std::bad_alloc (or possibly something else, compiler dependant) and they need to adjust their test codes to now catch bad_alloc which is problematic - given this might pepper over actual bad_allocs elsewhere in their code.  
  
Handling crazy code, as zero arguments as before yielded no bugs before. Thus imho, it is a bit crazy to change how crazy input is handled, and start throwing undefined things. Hence this PR should be pulled in.

---

## Comment 14

> Username: gpongelli  
> Created_at: 2019-01-08 21:39:26 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-452461345  

Hi, thanks for this fantastic set of libraries.  
  
Just for information, the case `argc == 0` is present into this example code from library release 1.69 https://github.com/boostorg/program_options/blob/d95d31684832075fda04c9fc916f8ec875552763/example/options_heirarchy.cpp#L171

---

## Comment 15

> Username: xnox  
> Created_at: 2019-01-11 10:22:02 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-453468592  

Please, please merge this =)

---

## Comment 16

> Username: xnox  
> Created_at: 2019-10-23 12:02:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-545410218  

Please, please, please merge this =)

---

## Comment 17

> Username: sehe  
> Created_at: 2023-12-16 14:53:12 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-1858836665  

People, this is the definition of politics. Please at least document the requirements. I prefer to land a patch like this (or #128...) so the UB is avoided. Remember the UNIX spirit: be liberal in what you accept, strict in what you output.  
  
If one is going to argue that such an interface is all-but-backwards ("not even used in C"), then just remove or deprecate it wholesale.

---

## Comment 18

> Username: vprus  
> Created_at: 2023-12-16 18:30:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/59#issuecomment-1858901102  

Hi,  
I am unable to parse your comment regarding "politics".   
  
In any case, this issue is superceded by #128.

---
