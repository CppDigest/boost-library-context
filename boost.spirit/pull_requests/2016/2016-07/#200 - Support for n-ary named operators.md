# #200 Support for n-ary named operators [Closed]

> Username: mxc-commons  
> Created at: 2016-07-27 23:21:05 UTC  
> Updated at: 2016-08-15 23:08:17 UTC  
> Closed at: 2016-08-15 17:49:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/200  

This PR adds support for n-ary named operators. They contribute to the nary_parser concept.  
  
The implementation extends the qi metagrammar to accept `proto::function` tags. If a `proto::function` tag gets encountered together with an appropriate specialization of `use_function` a  generator function named `make_function` gets called which produces a nary parser component.  
  
The parser class enabled here uses function style syntax. The general form is `op(p1, ...)`, where `op` is the name of the nary parser and `(p1, ...)` are a variadic number of arguments. Each argument must conform to the qi metagrammar, i.e. it has to be a qi parser expression.  
  
This PR is intended to add the capability to define n-ary named operators. It does not introduce a particular operator yet.  
  
A test case is provided with test/function.cpp. This file in particular implements a ternary conditional operator `if_`. Purpose of this implementation is to test if a nary operator gets created properly, i.e. that the additions to the qi core work properly. The other purpose is to demonstrate how nary named parsers get implemented and enabled via `use_function`.  
  
The test case compiles and runs without failure on msvc-9.0, msvc-12.0, msvc-14.0, gcc 6.1. The complete test suite does not compile currently as discussed already in PR #198. With PR #201 I provided a bug fix for the qi part of the test suite. The introduction of nary operators has no impact on the tests in their current state. What compiled and ran before nary operators where introduced continues to compile and run. No additional fails. With the PR #201 bugfix, the qi test suite compiles with no errors or failures.  
  
The only adjustment to the documentation is the What's New section which announces support for  
this parser class. This might be ok at this stage (without having a particular operator introduced).  
  
Here is a list of the particular changes:  
- Added to What's New documentation  
- Added an additional case for `proto::function` to the metagrammar.  
- Added `make_function` handler for this particular case.  
- Renamed function `make_binary_helper` to `make_nary_helper`. This helper function is suitable for both binary and nary operators.  
- Created test case function.cpp.  
- Added test case to jamfile.  
  
May I ask the community to please assess this pull request, the discussion, and if you are deep in qi the code provided, also? I would highly appreciate if you respond to this post and tell me, what you think.  
  
Here is what I think why this PR should be accepted:  
1. Currently the only option you have to write a qi operator, is to implement a parser and enable it using use_operator<>.  
     
    The arity is restricted to unary and binary and you have to associate your parser with a valid C++ operator like (examples: -y, x % y).  You can not write a parser operator with an arity > 2 with qi.   
     
   **With this PR you can write write operators of arbitrary (even variadic) arity. Fully C++03 compliant.**  
2. Qi already supports the nary_parser model in a way which is not per se restricted to unary and binary operators. That means if there was way to make an n-ary operator to be recognized by qi, qi would fully support it.   
     
   **This PR provides a way to make qi know about n-ary operator parsers. Not more. The existing code base is not touched.**  
3. The number of c++ operators available is limited. So currently the maximum number of unary parsers which can get implemented is equal to the number of unary c++ operators. The same applies for binary operators and binary c++ operators.  
     
   **With this PR the number of possible unary and binary parsers is not limited any longer.**  
4. If you ever implemented a qi operator parser, you may have found, that it is not quite easy to select a c++ operator suitable to look expressive and meaningful in terms of what your parser does.   
     
   **With this PR you can provide a meaningful name (even) for your unary or binary parser.**  
5. Another topic is operator precedence. If you write `a | b >> c % d`, then c++ operator  
   precedence applies, because this is valid c++ code getting executed. If you want  
   to implement a binary parser this makes the selection of an operator even more  
   difficult (see http://en.cppreference.com/w/cpp/language/operator_precedence).  
   Most of more "interesting" operators (those with higher prio (lower number)) are already used by qi.  
     
   **With this PR you can write operators which have a precedence of 2 (function call).**  
6. Making my first dives into qi I was looking in particular for differences between qi and X3. I also asked Joel, if qi is going to get deprecated. He said it is not. Therefore it makes sense to examine X3 for interesting things qi is lacking. I found the  expect directive was a good case to learn. I created one for qi (PR#196) which got accepted. Support for n-ary operators is more tricky, but it is also a feature  
   X3 supports.  
     
   See this example http://coliru.stacked-crooked.com/a/cb13c1d357cdc464. This was gently provided      by sehe as a part of one of my silly questions on stackoverflow.  
     
   **This PR closes a capability gap between qi and X3. As qi is not going to get deprecated, this seems  to be  important.**  
7. Rule evaluation control: qi automatically applies rules and that works good. Anyway, there seems to be (have been) a demand by users to gain some control about the rule evaluation control flow. Best example for that is the legendary Nabialek trick which is still referenced today.  
     
   This PR allows you to create a parser which you pass a variadic number of parsers in. Then your parser is in full control of what it does with the supplied argument parsers. You could even implement your own rule application algorithm.   
     
   In extreme: Create a parser, say my_engine, pass all your rules in and evaluate them to your liking completely bypassing the qi rule application algorithm. Without loosing any of qi's features. Maybe no one would actually want to drive that into the extreme. But there are always many approaches possible to solve a problem.   
     
   C++ offers so many different concepts to approach a problem. Control is up to the user. The user can decide and is not restricted to a particular programming paradigm or implementation by the programming environment.  
     
   **This PR increases the options you have to approach a problem using qi significantly.**  
8. Is there an alternative solution?  
     
   Qi binary and unary operators cover partly what this PR introduces. They cover the operator part of the functionality. But you can't name them, their number is limited to number of c++ operators available and their precedence depends on the c++ operator precedence they are associated with. Existing Qi operators are great, in particular operator notation is often quite beautiful. Qi operators have limitations which this PR addresses.  
     
   **This PR removes limitations restricting the implementation of unary and binary parser operators. Operators of arbitrary arity can not get emulated using existing qi features.**  
9. Does qi get more expressive?   
     
   Qi gets more expressive in terms of HOW things can get parsed. It gets more expressive in that operators of arbitrary (even variadic) arity can be defined. Which currently can not be done. There is no way to emulate an operator with variadic arguments with qi. The only option is cascading binary operators. `p1 >> p2 >> p3 >> ...`.  Depending on the particular implementation a native operator with variadic argument list can perform better.  
     
   Even with operator chaining you do not have a central point of control over the argument parsers. No `>>` does know of it's neighbour `>>`'s  second argument. They are not context aware.  
     
   **This PR allows to create context aware operators of arbitrary arity.**  
10. Windows-MSVC: I am on that platform and in most project contexts I do not have the choice to switch the compiler. Enterprises with PSPGs determining the full tool chain. So for me, and maybe to some of you, MSVC support is important. X3 is not necessarily aimed at full MSVC support (i.e. see discussion here: PR #168 ). I want to please ALL of my customers with THE SAME version of spirit. I can not afford and I do not see any justification to enable my people to support X3 for customers, who don't care about MSVC and qi for customers who do. If X3 does not support MSVC, it's a no go. So a modern qi is important.  
      
    **This PR enhances the capabilities of qi by bringing them closer to X3, which is important for people on MSVC.**  
11. What would be the risks imposed by merging this PR?  
      
    This PR touches the metagrammar, the heart of qi. This might frighten on the first sight. But much more than location does structure contribute to the possible risk imposed. Let's see:  
      
    The metagrammar extension with an additional switch for proto::tag functions does never fire that switch with existing qi code. Proof: If any of the existing qi constructs would have produced a `proto::tag function` (this would fire the switch), this code would not have compiled. You can try this out. So we can assume that none of the parser implementations out in the community produces a `proto::tag::function`. Therefore the new switch will not fire with any existing code. qed. This means in particular, existing code out there will produce exactly the same parsers as before. No reinterpretation possible.  
      
    If the switch never fires, the `make_function` generator function will never get triggered by existing code. Existing qi code can never instatiate a `function` parser by failure.  
      
    The renaming of `make_binary_helper` to `make_nary_helper` does not induce risk. The only reference to that was also changed. I changed the name, because this helper supports nary parser creation in general, not only in the particular binary case. I think this rename makes sense anyway. The new name is more precise.   
      
    So IMHO there is no risk for existing code. This assumes, that C++ is deterministic.   
      
    The only chance I see that the pure existance of this PR's code would harm existing code, is that it could reveal bugs which already existed in qi but did not show up (the compiler does something different, which may lead to a different memory layout, which could change something which was undefined but harmless to something undefined and harmful). Allthough that minimal risk exists, I believe it is neglectible. Reason: Qi compiles cross-platform cross-compiler. Such a sophisticated bug would have shown up on some platform on some compiler.   
      
    So the changes are fundamental. A new class of parsers is a fundamental enhancement. The change had to be done deep in the core. The metagrammar was extended by an additional switch.  But nothing else actually gets changed. Something is added which does not interfere with what was there already. The main reason that this solution could be done which seamlessly integrates in existing meta-grammar/meta-compiler code is, that qi is so well done, that support for n-ary operators was already available. Just like as if someone actually anticipated upcoming n-ary operators.  
      
    It remains the risk of using the new feature. But this only means any new `function` has to pass QA by passing tests as required. The same procedure as for all parsers.  
      
    **My conclusion:  This PR does not induce any significant risk.**  
12. Another risk: If qi stops to evolve, community support will erode.  
    **That's for sure.**  
13. Do we need this enhancement?   
    It depends on what you mean by "need". We don't need computers but life is better with them. Earth will not stop spinning without this PR. I'd love to see, that qi is not end of live, so:   
      
    **If it is true that qi is not going to get deprecated then what is important for X3 should be important for qi also. And considering the points above my answer is: Yes.**   
  
So, what do you think? Thank you for the time you spent on reading this. Any feedback would be highly appreciated. Did I miss something important? If you want to test this, checkout the develop branch of my fork of spirit. develop is actually the feature branch for this PR (I learned somewhat late about branching ;) (https://github.com/mxc-commons/spirit)  
  
Regards, Frank

---

## Comment 1

> Username: mxc-commons  
> Created_at: 2016-07-28 03:38:26 UTC  
> Updated_at: 2016-07-28 21:01:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-235792508  

With PR #201 the spirit_v2/qi  testsuite compiles successfully. All tests pass.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-07-28 12:01:12 UTC  
> Updated_at: 2016-08-13 04:54:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#discussion_r72608961  

Indentation

---

## Comment 3

> Username: Kojoley  
> Created_at: 2016-07-28 12:01:51 UTC  
> Updated_at: 2016-08-13 04:54:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#discussion_r72609022  

Why `::mxc::qitoo`?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2016-07-28 12:02:03 UTC  
> Updated_at: 2016-08-13 04:54:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#discussion_r72609047  

Same as above

---

## Comment 5

> Username: mxc-commons  
> Created_at: 2016-07-28 20:43:13 UTC  
> Updated_at: 2016-08-13 04:54:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#discussion_r72698811  

It is a reference to https://github.com/mxc-commons/MxcQitoo, where I publish qi extensions.

---

## Comment 6

> Username: mxc-commons  
> Created_at: 2016-08-01 02:19:42 UTC  
> Updated_at: 2016-08-01 02:22:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-236475220  

Is this one in consideration? X3 supports the implementation of n-ary operators. The way to go is somewhat different, but X3 on the other hand is not-proto. I believe, this PR introduces n-ary operators the way most "natural" for X2 and very compatible to older compilers (as long as boost::proto is).  
  
Here is an example of an n-ary operator in x3 http://coliru.stacked-crooked.com/a/cb13c1d357cdc464.  
Written by sehe. Thanks very much.   
  
For X3 is not going to support MSVC, I hope a request to further enable X2 gets considered. But if X3 was going to support MSVC, it's the same. Or is  X2 going to get deprecated? Then someone should say so.

---

## Comment 7

> Username: djowel  
> Created_at: 2016-08-01 02:38:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-236477075  

Oh definitely! Give me some time. Swamped right now.

---

## Comment 8

> Username: mxc-commons  
> Created_at: 2016-08-01 02:54:44 UTC  
> Updated_at: 2016-08-01 03:15:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-236478608  

Allthough I have plenty of time I could spend on spirit, there are people around me paying for that time. Our (current) mission: Gain some reputation around c++.I am as honest as I can be.   
  
Anyway, I  have to take care for  the people, who work hard paying my time to enable me to work against that goal. I could be fast if I was allowed to. If Spirit is the wrong place for being fast, just tell me. No problem. But like you I have goals to achieve. I am not "swamped" but I have  goals to achieve. I don't get them if nothing happens.  
  
If Spirit 2 is (going to get) depracted, please tell me. And I woul like to know the schedule of X3. When will it be the official replacement for X2? When will X2 support end?   
  
I do not want to offend you. These are questions regarding the product life cycle which "usually" (from my work experience, which does not have much to do with open source contribution) get examined before anything starts. As long as I do not know, if qi is worth any efforts, I am somewhat on standby and waiting which prevents me to engage somewhere else (where is hopefully more action).   
  
I do not want you to accept garbage. But at least some feedback would be helpful. If you think, this is garbage, just reject it and I'll close it.

---

## Comment 9

> Username: djowel  
> Created_at: 2016-08-01 03:15:55 UTC  
> Updated_at: 2016-08-01 03:19:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-236480588  

X2 (Qi) is not going to get deprecated. That's for sure. Anyway, what's the hurry? This is a new feature request and one that needs to be studied well.   
  
And talking in terms of honesty, I'd say that stability has a higher priority to me than new features. The recent errors that have surfaced, IMO is more important and I am hoping that more developers will pick up some of the maintenance tasks. I know that working on these things are boring and do not showcase one's c++ prowess. Nevertheless, it still has to be done.

---

## Comment 10

> Username: mxc-commons  
> Created_at: 2016-08-01 04:54:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-236489255  

I agree, any new feature request needs to checked carefully. I completely agree, that stability is more important than new features are.   
  
I completely understand, that you might be reluctant because of my header inclusion thing with the expect directive. I apologize for that. This was not necessary and could be avoided.  
  
But please understand me, too. I am trying my best to obey naming conventions which are not documented, trying to include the right headers to enable a minimum inclusion setup, where the requirements are quite unclear. Finally I am asked to test against a suite that does not compile. And it takes me time to find out, that this is simply the current release state. It does not compile and didn`t for ages, I think.  
  
I hope, you can agree that this is not the easiest setup to start with.   
  
Community? Qi is such a smart piece of work, that it should not be difficult to establich that. Qi is tremendous. It teaches you everything you ever wanted to know about C++ metaprogramming. Not just Qi, proto and fusion also. What I know about that I know from qi. Three months ago I did not even know (beyound trivials), what a  template is good for. Before boost 1.59.0 I did not know that boost existed. Too honest? I hope, this does not make my contributions even more suspicious. To manage a team it takes documented guidelines. These are   
  
So, if you want to accept me as a new community member (as said before: I'd be proud), please understand, that my mission is not to fix the test suite, which (from my point of view) is prabably be broken since boost 1.54.0.  
  
I suggested a bug fix for the spirit_v2 test suite. With this fix at least the qi test suite compiles (qi is the part of spirit I am currently interested in, not Karma, not Lex). This is another discussion, and I follow your approach to find an alternative better approach. Karma and Lex test suite still don't compile (for me not interesting currently). Additionally, the examples do not compile. It's not MSVC. gcc 6.1 fails as well.  
  
Allthough I would be proud to become an accepted community member, I would always say (as a first quote) to my companion community member: Eat your own dogshit. I am not here to care about issues which are present since stone age.   
  
Issues from boost 1.54.0: If it doesn_t compile, fix it. If it does not work, fix it. The fix I supplied for the "lying name" `is_not_variant` is as much as you can expect from a community member, who's primary goals have nothing to do with fixing other peoples bugs.  
  
 A simple reaction, that this PR is in consideration and  decision be been made till tt--mm--yyyy would have been enough. I am not annoyed. Just wondering why nothing happensl

---

## Comment 11

> Username: djowel  
> Created_at: 2016-08-01 06:32:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-236499907  

This week, promise :)   
  
That said, I'd urge you, and anyone else here listening in, to help contribute to the tests and maintenance. These are the boring things that often get neglected.

---

## Comment 12

> Username: mxc-commons  
> Created_at: 2016-08-02 04:34:25 UTC  
> Updated_at: 2016-08-02 18:27:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-236794492  

Lazy instantiation works. I added a test case.

---

## Comment 13

> Username: djowel  
> Created_at: 2016-08-05 22:16:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-237978892  

OK, I did evaluate the PR. The code looks good, and I think you know Spirit very well, and definitely your C++. It comes with a high risk though, since it touches the very core of Spirit -- the proto metagrammars. And that's why it's taking time for me to decide. It is a new feature, that changes the Spirit meta-grammar. A big change in fact, with a decade of syntax stability since Qi/Karma came to be. Typically for new features, we place it in a staging ground first: the Spirit repository. In this case, the changes are so fundamental that it can't be in the repository, hence I find myself in an uneasy situation.  
  
So, with a very heavy heart, I have to reject this PR for now. That is a soft rejection though and I can easily be swayed. What I need is for you to drum up support for this feature; why we need it and what would be the benefit of this over alternative (possibly existing) solutions. I need consensus, and that's not what we have right now that it's only between you and me. I'll need you to ask the Spirit (and or Boost list) list for interest first, before we can proceed.

---

## Comment 14

> Username: sehe  
> Created_at: 2016-08-08 23:48:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238412273  

I concur with @djowel : what's the hurry? If you want to establish a reputation, I believe you have already done so. (I can only wonder what kind of audience would say "But it's not in the tar-ball release, so it doesn't count".)  
  
Spirit is a production library for generating parsers from a C++ eDSL. It has been for over 15 years.   
  
Responding to some points  
  
> This PR is intended to add the capability to define n-ary named operators. It does not introduce a particular operator yet.  
  
A motivating example would help a lot. I don't disagree with the abundance of arguments you give  explaining how it will complement the current EDSL expressiveness, but it's all pretty abstract. That makes it uncompelling (if we've lived without it for all these years...).  
  
> With the PR #201 bugfix, the qi test suite compiles with no errors or failures.  
  
Thank you for that!  
  
> Rule evaluation control: qi automatically applies rules and that works good.  
> Anyway, there seems to be (have been) a demand by users to gain some control  
> about the rule evaluation control flow. Best example for that is the legendary  
> Nabialek trick which is still referenced today.  
  
I had trouble seeing how this relates to the PR. If I understand you correctly, I think Qi has had a few directives like this for some time. `repository::qi`'s `confix`, `distinct` and `keyword list` operators come to mind. (see [here](http://www.boost.org/doc/libs/1_61_0/libs/spirit/repository/doc/html/index.html)).   
  
(Within Qi core `qi::hold`, `qi::lazy`, `qi::as<>`, `qi::matches` all act as "higher order" directives that control the operation of the subsidiary parsers. I suppose the major significant difference would - again - be the arity of the parser arguments a directive can take. There seem to always have been ways around that if you look at the linked Repository samples.)  
  
> So IMHO there is no risk for existing code. This assumes, that C++ is deterministic.  
  
Being deterministic doesn't prevent surprises. ADL, partial ordering, etc. are prime sources of such surprise¹. I think there have been a number of occasions in which Qi had evolved to a state where certain ill-formed/meaningless constructs would compile, with fairly arbitrary results. In most cases, these could be fixed by adding more diagnostic checks.   
  
> Another risk: If qi stops to evolve, community support will erode.  That's for sure.  
  
That's a pretty obvious hyperbole. If Qi stops to be maintained, it will rot.  
  
But as it is, it is a mature library with many existing production usages. All that is required for it to stay relevant is for compiler support to be maintained while the supporting Boost libraries evolve. The evolution of variant, optional, fusion, MPL etc. have already vastly benefited Qi without requiring radical innovation to the concept of Qi.  
  
I think stability is a core feature of most Boost libraries.  
  
> If it is true that qi is not going to get deprecated then what is important for  
> X3 should be important for qi also. And considering the points above my answer  
> is: Yes.  
  
I find it hard to argue that things that Qi hasn't needed for a decade are important in X3. It's just notably easier to functionally compose parsers in X3/C++14. Possible != essential.   
  
That possibility in X3 stems from simplicity.  
  
Simplicity **is** essential.  
  
What matters most, I think, is whether this PR contributes to/maintains simplicity. It seems like it does, but I'll have to find time for a closer look.  
  
Cheers,  
Seth  
  
---  
  
¹ There's a reason that Eric Niebler went to great lengths to "formalize" a pattern for declaring global function object instances in template library code, just to avoid it.

---

## Comment 15

> Username: mxc-commons  
> Created_at: 2016-08-09 02:21:59 UTC  
> Updated_at: 2016-08-09 18:03:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238435123  

Thank you so much. :) As always your answer lights up my day (or night respectively ;)  
  
> I concur with @djowel : what's the hurry? If you want to establish a reputation, I believe you have already done so. (I can only wonder what kind of audience would say "But it's not in the tar-ball release, so it doesn't count".)  
>   
> Spirit is a production library for generating parsers from a C++ eDSL. It has been for over 15 years.  
  
To be completely honest, it's not only about reputation. Qi is important for our business. Enterprise customers nowerdays are open to Open Source OS platforms, but is still a hard  discussion to bring a dependency to a open source library in. It's less difficult, if it is labelled Boost (if you are talking to architects and coders, and not to managers, what I usually do). If an Open Source Library, then preferrably the unmodified release. Therefore my interest to contribute to the "standard" release.  
  
What's the hurry? Ok, that's related to business which is fast. You have an argument or you do not have it. I'd just like to know what arguments I have. Lying is not our business.  
  
> A motivating example would help a lot. I don't disagree with the abundance of arguments you give explaining how it will complement the current EDSL expressiveness, but it's all pretty abstract. That makes it uncompelling (if we've lived without it for all these years...).  
  
In the mailing list I introduced an example. But I'm not sure, if it's motivating enough. I took the example from our [discussion on stackoverflow](http://stackoverflow.com/questions/37669936/spirit-qi-how-can-i-write-a-nonterminal-parser). Implement a longest_match parser. I referenced the X3 solution you provided several times since then. Even in this discussion. (BTW: I think, you are the best witness, of my personal progress with qi :)  
  
But, honestly, I did not expect that a particular example was necessary. This PR introduces a new class of parsers, new and (hopefully) exciting capabilities to program and extend Qi. I'd be happy if this would unleash the user's creativity. (Users = people willy and able to write own custom parsers (with my full support, if wanted)).  
  
Are we talking on the user level? On the maintainance level? On the architects level? I assumed the latter.    
  
> With the PR #201 bugfix, the qi test suite compiles with no errors or failures.  
> Thank you for that!  
  
Not for that. I propose changes and need a test suite that compiles as a minimum requirement. The PR was not accepted. @djowel wants to provide a better fix himself.  
  
> Rule evaluation control: qi automatically applies rules and that works good.  
> Anyway, there seems to be (have been) a demand by users to gain some control  
> about the rule evaluation control flow. Best example for that is the legendary  
> Nabialek trick which is still referenced today.  
>   
> > I had trouble seeing how this relates to the PR. If I understand you correctly, I think Qi has had a few directives like this for some time. repository::qi's confix, distinct and keyword list operators come to mind. (see here).  
> >   
> > (Within Qi core qi::hold, qi::lazy, qi::as<>, qi::matches all act as "higher order" directives that control the operation of the subsidiary parsers. I suppose the major significant difference would - again - be the arity of the parser arguments a directive can take. There seem to always have been ways around that if you look at the linked Repository samples.)  
  
None of the directives you mention intrude their operand parser's or share knowledge with them. They are all about pre-processing, post-processing, instantiation of parser which are black boxes for the "surrounding" directive.  
  
I guess, it's getting too abstract again. The n-ary operator proposed here handles the argument parsers as black boxes, too. But it decides what argument is executed or skipped, what attribute is taken or rejected (even if parsing of that argument parser succeeded) and so on. It implements it's own parsing logic where the argument parsers are components with hidden implementation.   
  
> So IMHO there is no risk for existing code. This assumes, that C++ is deterministic.  
>   
> > Being deterministic doesn't prevent surprises. ADL, partial ordering, etc. are prime sources of such surprise. I think there have been a number of occasions in which Qi had evolved to a state where certain ill-formed/meaningless constructs would compile, with fairly arbitrary results. In most cases, these could be fixed by adding more diagnostic checks.  
  
Ok. I know about the potential in general. But what can we expect of ADL, partial ordering, etc regarding surprises? If you look at this particular case statement, do you actually see room for such surprises? I took that into consideration. I can't see potential issues. What am I missing?  
  
> Another risk: If qi stops to evolve, community support will erode. That's for sure.  
>   
> > That's a pretty obvious hyperbole. If Qi stops to be maintained, it will rot.  
  
Touché. :) As it is written, this is completely rhethoric.  
  
What I mean, Qi seems to stagnate somewhat. I don't think of new features but of maintenance. The test suite does not compile without error. The examples do not compile without error. On the other hand, there were several boost releases since the last time the tests and examples were touched. Joel urges the community, that maintenance is important. That's not enough. It's difficult for me to promote a library with a broken test suite.    
  
> But as it is, it is a mature library with many existing production usages. All that is required for it to stay relevant is for compiler support to be maintained while the supporting Boost libraries evolve. The evolution of variant, optional, fusion, MPL etc. have already vastly benefited Qi without requiring radical innovation to the concept of Qi.  
>   
> I think stability is a core feature of most Boost libraries.  
  
Ok, but very general. I do not think that all desirable evolution for qi will happen as a sideeffect of other library's evolution. Stability is one of the core features of boost. But there are others. If stability was the only criteria, nothing needed to happen once the thing was stable. There would not be any need for X3. So innovation and future awareness may be other important boost features. I do not see particular reason, why X3 is allowed to evolve and Qi not. X3 currently is more risk per se as Qi would be with this PR merged.  
  
> If it is true that qi is not going to get deprecated then what is important for  
> X3 should be important for qi also. And considering the points above my answer  
> is: Yes.  
> I find it hard to argue that things that Qi hasn't needed for a decade are important in X3. It's just notably easier to functionally compose parsers in X3/C++14. Possible != essential.  
  
I completely agree. Possible is almost everything. But essential? I completely agree, that each feature X3 has and Qi has not has to be considered very carefully for a back-port. If a backport was only possible if Qi needed to be changed breaking it's concepts, design paradigms, etc, it should not be done. That's for sure the area where X3 will be superior to Qi with all legitimation. I would never turn Qi inside out to introduce something which is somewhat natural for X3 but almost impossible for Qi. Everything is possible, not all of that is a good idea.   
  
But if a backport can be done maintaining philosophy, paradigms, concepts I urge that this gets considered seriously. Doing something is a risk. Not doing something is a risk also. Not everybody is aware of that. If I stand up now to get another Wodka/Lemon I could fall down the stairs. If I don't, I could die because the hardware test environment next to my place will explode and take me with. In business terms: Almost nobody is aware of the price he has to pay for non-investment. Every decision costs. Keeping the Status Quo costs. I put much efforts in this discussions. Because I believe, nobody thought about the PNI yet (the price of non-investment).  
  
Conclusion: This PR backports the ability to create n-ary operators. Completely different to the X3 approach, but completely congruent with the Qi approach. It's low impact. It simplifies.  
  
> That possibility in X3 stems from simplicity.  
> Simplicity is essential.  
> What matters most, I think, is whether this PR contributes to/maintains simplicity. It seems like it does, but I'll have to find time for a closer look.  
  
If u mean, usability I agree. Usabality for programmers in particular. AFAIK there are not much people out there who implement custom parsers. One important reason for that IMHO is that there is no documentation. The only guide how to implement a custom parser is the [iter_pos parser](http://boost-spirit.com/home/articles/qi-example/creating-your-own-parser-component-for-spirit-qi/). This article found great response but raised more questions than it answered. Life gets much easier for custom parser implementors with that PR (I mean, solving problems not currently solvable easily). How to implement a parser in general is a lacking documentation topic. (I could work on that, but as "What is it good for? We did not need it for a decade." applies to every new thing ...)  
  
From an end user perspective, simplicity is topic as well. I believe, users are at least c++ programmers, so I am sure, that they have an immediate idea what max(a,b,c,d) does. Without that PR a complicated solution could be programmed which would end up in a user interface looking something like max[ a += b += c += d ], which would do the same as max(a,b,c,d) does with that PR.   
  
I would be happy if you would take the time to look into the code (no more than 30 lines or so (without the test)).  
  
There are a lot of maintainance tasks open. There are even bugs open (like the single member struct problem). If the community is only asked do maintenance tasks which were obviously not important for the last years...   
  
I think, we are still in tryout phase. Working together needs at least some level of trust. As you know from our stackoverflow conversation, I have the implementation for this PR ready since June, 14th. Since then I thought about it's value, tried to figure out potential risks imposed, thought about compliance to Qi's concepts, tested and did and made until I was convinced, that this one would and should make it. One reason: I am pretty sure, everybody experienced as you are, can completely oversee the potential, the risk and all. And we could talk about particular issues, if I missed something.  
  
I did not expect to have to talk about truisms. Yes there things like ADL, partial ordering and what, which are difficult. But looking at  this particular implementation, will there be ADL dragons spitting fire? Hey, it's not magic. This can be answered. At least with a probability. I say No. This is harmless.  
  
My expect directive got accepted after discussions, that I was not comforming to (undocumented) standards. My simple bugfix was not. This PR will not be. What would in particular happen to any fix I'd provide? If the risk to change something is always higher rated  than the chance to fix/enhance something things get difficult. If you had the choice to change nothing by doing nothing or to change nothing with notifiable effort in time and designation, what would you choose? I am on the do nothing side. Minimum effort. Same result. So why try to ride a dead horse?   
  
Fixing the single-member bug for example, which definitely can be done (I did it, I am still thinking about the risk, the value, etc), would have much more systemic impact than my current PR has. If even this simple POS of PR has no chance because of risk, there seems to be no room to raise a PR for such a bug fix.  
  
POS: Piece of Shit ;)

---

## Comment 16

> Username: mxc-commons  
> Created_at: 2016-08-09 23:01:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238718539  

To illustrate how simple it is to implement a longest_parser with that PR I added an implementation for longest now for review.  
  
There are now to examples/use cases:  
1. The if_parser, which implements a ternary conditional operator taking three arguments.  
2. The longest_parser, which returns the match where parsing consumes most of the input. It takes a variadic number of argument parser.  
  
I added one single test case at the top of the test sequence to illustrate longest.

---

## Comment 17

> Username: djowel  
> Created_at: 2016-08-09 23:09:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238720054  

Here's how you do if/else in Qi:  
  
```  
eps(cond) >> a | b  
```

---

## Comment 18

> Username: Kojoley  
> Created_at: 2016-08-09 23:14:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238720962  

> Here's how you do if/else in Qi:  
>   
> ```  
> eps(cond) >> a | b  
> ```  
  
Actually it will be `eps(cond) >> a | eps(!cond) >> b` if you do not want the situation when `b` is tried if `a` fails, or I am wrong?

---

## Comment 19

> Username: djowel  
> Created_at: 2016-08-09 23:19:53 UTC  
> Updated_at: 2016-08-09 23:20:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238721950  

Good point. Or in many cases:   
  
```  
eps(cond) > a | b  
```

---

## Comment 20

> Username: Kojoley  
> Created_at: 2016-08-09 23:25:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238722991  

Most of my cases were `eps(cond) >> a | eps(!cond) >> b`, and I was very sad when `cond` is a call to a phoenix function, because of that (to avoid double call) I have to use local variables.

---

## Comment 21

> Username: djowel  
> Created_at: 2016-08-09 23:25:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238723000  

Spirit (Classic) used to have a lot of parsers like if and while. Here's how you do while in Qi:  
  
```  
*(eps(cond) >> p)  
```  
  
(Aside: longest is a directive in Spirit Classic i.e. longest[a | b | c...] ), but we chose not to add it back to Qi for performance reasons --it is a bad practice, IMO most often than not, you don't need it).

---

## Comment 22

> Username: djowel  
> Created_at: 2016-08-09 23:28:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238723364  

Kojoley, agreed. And it might be a compelling reason enough to have a native if construct.

---

## Comment 23

> Username: mxc-commons  
> Created_at: 2016-08-09 23:59:15 UTC  
> Updated_at: 2016-08-10 00:05:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238728735  

I think, there are design decisions which should be at least possible for the user. You definitely do not need the `%` operator provided by qi, because it's semantics can be expressed easily without it. It's convenience. It's readability.   
  
If something can get offered to the users, which gives them greater flexibility in terms of "custom solutions possible and supported by qi", than I suggest to consider that.  
  
If flexibility, simplicity, solution space, etc can be widened without violating core Qi concepts, dos and donts, C++ standard compliance, philosophy, I think that's worth serious consideration.  
  
But that may be depend on the underlying paradigm. C++ for example allows you to implement Fibonacci in a thousand ways: FP, OO, template, preprocessor, almost unstructured, ... . There are best practices for sure, but it's the implementors choice which way to go. He is even free to decide for bad practice. That's one of the core values why C++ is awesome (for me).  
  
N-ary operators add another blade to the Swiss Army Knife Qi already is making it even more flexible.

---

## Comment 24

> Username: mxc-commons  
> Created_at: 2016-08-10 00:19:51 UTC  
> Updated_at: 2016-08-10 00:52:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238731964  

I wanted to give an example. Longest is most often not needed, if the user wants to take care about rule ordering etc. Longest can not perform as fast as alternative can. That's the price for convenience. If I was to decide to introduce longest, I would require a detailed documentation of all that aspects. Pros and Cons. Convenience vs. Speed. Usage recommendations. Best Practices.  
  
The educated user then could make a qualified decision to take it or leave it knowing exactly what he gets and what price he has to pay. Given that, I could accept longest. In most cases I would not use it. But there may be rare cases where I'd be happy to have it. I do not know.  
  
This PR is not about longest anyway.

---

## Comment 25

> Username: mxc-commons  
> Created_at: 2016-08-10 00:48:30 UTC  
> Updated_at: 2016-08-10 00:54:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238736059  

Please have a look at the implementation of the operator `/` and `kwd` (`ikwd`, ...) directive(s) from the repository. It shows that an implementation which maintains a shared context over an arbitrary number of argument parser was/is all but trivial currently.  
  
From the docs: "The keyword list operator works tightly with the kwd and ikwd directive and can not be used without it." Ok, that's somewhat non-Qi (for me at least) but this was to accept to provide a working solution.  
  
This is a very engaged and sophisticated approach to make something possible Qi did not genuinely support. Hat off. Deep respect!!  
  
With this PR in place when they did it it would have been much easier.

---

## Comment 26

> Username: mxc-commons  
> Created_at: 2016-08-10 01:17:21 UTC  
> Updated_at: 2016-08-10 01:21:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-238740014  

How was classic qi's longest called? I could not find it in the headers.

---

## Comment 27

> Username: mxc-commons  
> Created_at: 2016-08-10 23:38:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239037119  

Yesterday I talked about the `/`and `kwd` combo from the qi repository.   
  
I added a first version of a "nabialek" operator, which provides similar functionality utilizing a n-ary operator.  
1. Simplicity of code  
2. Compile time reduction  
3. Self-contained (no dependencies to other parser components)  
4. Interacts tranparently with other qi components (like Kleene Star, Plus, repeat), so the internal repitition mechanisms got obsolete.  
5. Runtime performance is a todo. Next exit: Workbench. :)  
  
The tests I provide are mainly for demonstration purposes. To test that seriously more needs to be done.  
  
Note: This version still does some qirks with the return attribute. The variant is internally always `std::string`. Please do not laugh at me. I will look after that. Nevertheless, I think what this thing is intended to show, it already does.  
  
And, I am not convinced that this approach is the finest one to implement nabialek. Coupling of parsers and keywords is not tight enough for my taste. But I wanted the code to be recognizable similar to the operator/directive combo.

---

## Comment 28

> Username: mxc-commons  
> Created_at: 2016-08-11 02:13:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239057408  

You can argue, that I simplified somewhat too much. I left out the keyword specific options the repo parsers provide. Instead of just an index a parameter set coud be provided for each keyword.

---

## Comment 29

> Username: Kojoley  
> Created_at: 2016-08-11 09:54:51 UTC  
> Updated_at: 2016-08-11 09:55:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239118619  

@djowel If native if parser is going to be added to spirit, please consider `if_(cond)[a][b]` form rather then `if_(cond, a, b)`. I think the first form is more spirit-style then the second, and it looks twenty times better when `a` and `b` is the complex rules.  
  
As I previously said, I felt the need of the if parser that is why I had written myself `toggle` parser. It is just if-then part (without else clause), and it is equal to `eps(cond) >> a | eps(!cond) >> eps(true)`, but it saved a lot of boilerplate code for me. Consider this example:  
  
```  
    >> toggle(cond_a)[a]  
    >> toggle(cond_b)[b]  
    ...  
    >> toggle(cond_n)[n]  
```  
  
where `cond` could be `_r1 & 0x01` or even `phx::bind(&some_flags_t::is_some_feature, _a)`

---

## Comment 30

> Username: mxc-commons  
> Created_at: 2016-08-11 17:10:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239226132  

maxence business consulting gmbh  
Frank Hein  
Geschäftsführer  
Am Weißen Stein 1  
41541 Dormagen  
T  +49 2133 - 2599-10  
F  +49 2133 - 2599-29  
M +49 151 - 54742989  
E frank.hein@maxence.de  
  
> Am 11.08.2016 um 11:55 schrieb Nikita Kniazev notifications@github.com:  
>   
> @djowel If native an if parser is going to be added to spirit, please consider if_(cond)[a][b] form rather then if_(cond, a, b). I think the first form is more spirit-style then the second, and it looks twenty times better when a and b is the complex rules.  
>   
> I think it would take more modifications to the core to enable that because of the two subscripts. I think currently this would get treated as directive subject + semantic action (?). If that was true, maybe you can exploit that to make your vision of if_ true.  
> As I previously said, I felt the need of the if parser that is why I had written myself toggle parser. It is just if-then part (without else clause), and it is equal to eps(cond) >> a | eps(!cond) >> eps(true), but it saved a lot of boilerplate code for me. Consider this example:  
>   
> ```  
> >> toggle(cond_a)[a]  
> >> toggle(cond_b)[b]  
> ...  
> >> toggle(cond_n)[n]  
> ```  
>   
> where cond could be _r1 & 0x01 or even phx::bind(&some_flags_t::is_some_feature, _a)  
>   
> That's beyond the capabilities of the if_ example. It expects cond to be a parser.  
  
The if_ parser here was only provided as documentation by example what to do to implement a nary operator. It wanted to make it simple and not too meaningless.  
  
But this one should not be considered to become a feature.  
  
> —

---

## Comment 31

> Username: Kojoley  
> Created_at: 2016-08-11 17:52:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239238070  

> I think it would take more modifications to the core to enable that because of the two subscripts. I think currently this would get treated as directive subject + semantic action (?).  
  
What about `if_(cond)[a].else_[b]` parser, [like this one](http://www.boost.org/doc/libs/master/libs/phoenix/doc/html/phoenix/modules/statement/___if_else_____statement.html) from the phoenix library?  
  
> It expects cond to be a parser.  
  
I think that is a nice feature to have, it covers such case: `rule[_c = _1] >> if(_c)[a][b]`  
  
> The if_ parser here was only provided as documentation by example what to do to implement a nary operator. It wanted to make it simple and not too meaningless.  
  
Consider renaming it to have the same name and signature as [`if_else(c, a, b)`](http://www.boost.org/doc/libs/master/libs/phoenix/doc/html/phoenix/modules/operator.html#phoenix.modules.operator.ternary_operator) in the phoenix library.

---

## Comment 32

> Username: djowel  
> Created_at: 2016-08-11 20:08:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239276332  

That is exactly the syntax I have in mind, Kojoley. Either will do. And this is also the main reason for my hesitation with the 'function' syntax. It goes against the "Spirit" style syntax that's been with us for a decade and a half now. The parens are typically used for things like passing inherited attribute and parameters to some parsers, and for that, there's no need for a core change. Parsers such as repeat, the rules and some primitives such as char_ and eps already do that. So there's this conflict of syntax that really bothers me. Syntax matters.

---

## Comment 33

> Username: Kojoley  
> Created_at: 2016-08-11 20:52:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239288781  

I think that `if_(cond)[a].else_[b]` is even more spirit-like and significantly more powerful. It allows using only if-then part with semantic actions `if_(cond)[a][sem_action]` (`sem_action` executes only when _then_ clause is executed).  
  
But there is one issue with it - should it work as `if_(cond)[a][sem_action_a].else_[b][sem_action_b]` (`sem_action_a` executes only when _then_ clause is executed, and `sem_action_b` executes only when _else_ clause executed) or as `if_(cond)[a].else_[b][sem_action]` (`sem_action` executes always)?  
  
The second one should not work as `if_(cond)[a][sem_action_a].else_[b][sem_action]` (`sem_action_a` executes only when _then_ clause is executed, and `sem_action` executes always) and should fail when the user tries to do this, or it can work this way: `if_(cond)[a][sem_action_a].else_[b][sem_action_b]` when this form is used and as `if_(cond)[a].else_[b][sem_action]` when there is no semantic actions on _if-then_ clause. The behavior of the last variant is more error prone, though more powerful.

---

## Comment 34

> Username: mxc-commons  
> Created_at: 2016-08-11 20:56:50 UTC  
> Updated_at: 2016-08-11 21:50:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239289864  

> > That is exactly the syntax I have in mind, Kojoley. Eitler will do. And this is also the main reason for my hesitation with the 'function' syntax. It goes against the "Spirit" style syntax that's been with us for a decade and a half now. The parens are typically used for things like passing inherited attribute and parameters to some parsers, and for that, there's no need for a core change. Parsers such as repeat, the rules and some primitives such as char_ and eps already do that. So there's this conflict of syntax that really bothers me. Syntax matters.  
  
In c++ () is for construction and operator() / callable and function(). And ambiguities with Most Vexing Parse.  
  
In qi there are constructs like `as<int>()[p]`.   Instead of `as<int>(p)`.  
  
maxence business consulting gmbh  
Frank Hein  
Geschäftsführer  
Am Weißen Stein 1  
41541 Dormagen  
T  +49 2133 - 2599-10  
F  +49 2133 - 2599-29  
M +49 151 - 54742989  
E frank.hein@maxence.de  
  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 35

> Username: teajay-fr  
> Created_at: 2016-08-11 21:00:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239290972  

I agree with Joel on the fact that syntax is very important to make writing parsers clear, consistent and elegant. Adding a function like syntax would introduce some inconsistency in respect of the other spirit constructs. This is I think a matter of taste, more than a technical matter.  
  
On the other hand, if I didn't miss anything, it should be possible to implement the n-ary function creation in the repository. If that is the case, it wouldn't compromise the rule definition consistency too much. And it would help users implement very specific parser behaviors using the function style syntax.  
  
We had a very similar discussion with Joel at the time I implemented the keyword parser, and in the end the implementation moved the repository. I don't think this was a bad decision. The core stays core and handles about 95% of the parser writing use cases. For the last 5% where very specific parsers need to be written, some tools exist in the repostory to help out. This is the line that has been followed for some years, and has worked out very well.

---

## Comment 36

> Username: djowel  
> Created_at: 2016-08-11 21:04:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239291978  

@teajay-fr, alas, this one touches the core proto metagrammar, hence making it a very hard decision. Once we allow it, there's no turning back.

---

## Comment 37

> Username: mxc-commons  
> Created_at: 2016-08-11 21:04:30 UTC  
> Updated_at: 2016-08-11 21:32:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239292074  

> > I think that if_(cond)[a].else_[b] is even more spirit-like and significantly more powerful. It allows using only if-then part with semantic actions if_(cond)[a][sem_action](sem_action executes only when then clause is executed).  
  
if(cond)[a [sem-action_a]].else[b[sem_action_b]] ?

---

## Comment 38

> Username: Kojoley  
> Created_at: 2016-08-11 21:07:40 UTC  
> Updated_at: 2016-08-11 21:10:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239292928  

> if(cond)[a [sem-action_a]].else[b[sem_action_b]]  
  
Yes, but we still need consistency with the other parsers (they accept `parser[a][sem_action]`, so why `if_[a].else_[b]` should not). Or it was a vote about allowing `if_[a].else_[b][sem_action]` and not `if_[a][sem-action_a].else_[b][sem_action_b]`?  
  
Syntax really matters a lot.

---

## Comment 39

> Username: djowel  
> Created_at: 2016-08-11 21:12:13 UTC  
> Updated_at: 2016-08-11 21:12:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239294124  

> if(cond)[a [sem-action_a]].else[b[sem_action_b]] ?  
  
```  
if_(cond)[ a[fa] ].else_[ b[fb] ]  
```  
  
to make it clearer. You see, the biggest advantage is the modular nature of this construct.  
  
> Yes, but we still need consistency with the other parsers (they accept parser[a][sem_action], so why if_[a].else_[b] should not)  
  
That is cool too, IMO. All those can be done without touching the core metagrammar.

---

## Comment 40

> Username: mxc-commons  
> Created_at: 2016-08-11 21:31:15 UTC  
> Updated_at: 2016-08-11 21:38:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239299201  

> `if_(cond)[ a[fa] ].else_[ b[fb] ]`  
>   
> to make it clearer. You see, the biggest advantage is the modular nature of this construct.  
>   
> Yes, but we still need consistency with the other parsers (they accept parser[a][sem_action], so why if_[a].else_[b] should not)  
> That is cool too, IMO. All those can be done without touching the core metagrammar.  
  
I understand that semantic actions follow directly on a parser to which they attach. Maybe I am wrong, but is  
  
`qi::lexeme[ qi::char_ [semaction_char]][semaction_lexeme]`   
  
not already possible? So    
  
`if_(cond)[ a[fa] ].else_[ b[fb] ] [f_if]` would have to be, too?

---

## Comment 41

> Username: djowel  
> Created_at: 2016-08-11 21:33:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239299680  

> I understand that semantic actions follow directly on a parser to which they attach. Maybe I am wrong, but is  
>   
> lexeme[ qi::char_ [semaction_char]][semaction_lexeme]  
>   
> not already possible? So  
>   
> if_(cond)[ a[fa] ].else_[ b[fb] ] [f_if] would have to bee, too?  
  
Yes, and yes.

---

## Comment 42

> Username: mxc-commons  
> Created_at: 2016-08-11 21:37:14 UTC  
> Updated_at: 2016-08-11 21:40:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239300742  

> Adding a function like syntax would introduce some inconsistency in respect of the other spirit constructs.   
  
@teajay-fr, please help me understand: What do you mean by inconsistency? Is it that we have constructor parenthesis and function paranthesis as opposed to now where we have constructor parenthesis only? (forgetting intentiously about addl members like e.g. symbols has)

---

## Comment 43

> Username: djowel  
> Created_at: 2016-08-11 21:38:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239301077  

> In qi there are constructs like as<int>()[p].   Instead of als<int>(p).  
  
als<int>(p) is possible with c++14 Variable templates. Perhaps with X3.

---

## Comment 44

> Username: mxc-commons  
> Created_at: 2016-08-11 21:49:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239303896  

> > So `if_(cond)[ a[fa] ].else_[ b[fb] ] [f_if]` would have to be, too?  
>   
> Yes, and yes.  
  
And cond could be a parser also?

---

## Comment 45

> Username: djowel  
> Created_at: 2016-08-11 21:51:44 UTC  
> Updated_at: 2016-08-11 21:53:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239304512  

> Adding a function like syntax would introduce some inconsistency in respect of the other spirit constructs.  
> @teajay-fr, please help me understand: What do you mean by inconsistency? Is it that we have constructor parenthesis and function paranthesis as opposed to now where we have constructor parenthesis only? (forgetting inentiously about addl members like e.g. symbols has)  
  
As much as possible, as a general rule, we should add new features without introducing new syntax and touching the core metagrammar. By doing so, we force consistency with the way we do things and minimize unforeseen gotchas later. The parens are currently being used simply to pass arguments (support/terminal.hpp and rule inherited attributes come to mind), not as a general construct.  
  
Also, consider the symmetry of Qi and Karma. If you add nary to QI, you should also be prepared to add them to Karma.

---

## Comment 46

> Username: djowel  
> Created_at: 2016-08-11 21:53:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239305043  

> So if_(cond)[ a[fa] ].else_[ b[fb] ] [f_if] would have to be, too?  
> Yes, and yes.  
> And cond could be a parser also?  
  
No.

---

## Comment 47

> Username: mxc-commons  
> Created_at: 2016-08-11 22:04:31 UTC  
> Updated_at: 2016-08-11 22:31:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239307683  

Ok, this would then needed to be considered for Karma to  maintain the duality. As a next step.  
  
The work for proto::function support was halfways done already when I started on that. use_function was already there.  More important, this one which I use was already there (home/qi/meta_compiler.hpp). It could be savely removed witout this PR.   
  
One could think,n-ary operators were started and forgotten to complete. I just finished them.  
  
```  
    // Qi function meta-compiler  
  
    template <>  
    struct make_component<qi::domain, proto::tag::function>  
    {  
        template <typename Sig>  
        struct result;  
  
        template <typename This, typename Elements, typename Modifiers>  
        struct result<This(Elements, Modifiers)>  
        {  
            typedef typename  
                qi::make_composite<  
                    typename remove_const<typename Elements::car_type>::type,  
                    typename Elements::cdr_type,  
                    typename remove_reference<Modifiers>::type  
                >::result_type  
           type;  
        };  
  
        template <typename Elements, typename Modifiers>  
        typename result<make_component(Elements, Modifiers)>::type  
        operator()(Elements const& elements, Modifiers const& modifiers) const  
        {  
            return qi::make_composite<  
                typename remove_const<typename Elements::car_type>::type,  
                typename Elements::cdr_type,  
                Modifiers>()(elements.cdr, modifiers);  
        }  
    };  
```

---

## Comment 48

> Username: mxc-commons  
> Created_at: 2016-08-11 22:22:42 UTC  
> Updated_at: 2016-08-11 22:29:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239311654  

> So if_(cond)[ a[fa] ].else_[ b[fb] ] [f_if] would have to be, too?  
> Yes, and yes.  
> And cond could be a parser also?  
>   
> No.  
  
I had to try. :)

---

## Comment 49

> Username: mxc-commons  
> Created_at: 2016-08-11 22:52:53 UTC  
> Updated_at: 2016-08-11 23:16:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239317510  

@teajay-fr: I created a benchmark for the example niabialek operator. I wanted to compare against the kwd / operator. I do not expect, that my implementation is necessarily faster, but I would start thinking, if it was significantly slower. Wanted to find that out.  
  
Anyway, I could not make your kwd and / operator compile. The example you provided in spirit/repository/example/qi does not compile, too. Also the test provided does not.   
(msvc-9.0, msvc-12.0, msvc-14.0, gcc-6.1.0). There is even no jamfile for the tests.  
  
Could you please have a look at that?

---

## Comment 50

> Username: mxc-commons  
> Created_at: 2016-08-12 04:52:39 UTC  
> Updated_at: 2016-08-13 04:45:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239360675  

I finally made `kwd` and `/` compile. You need to `#define BOOST_RESULT_OF_USE_TR1` in order to compile it.   
  
Here are the results on my machine (3 keywords, 6 matches in input, msvc-14). With only 3 keywords none of the implementations delivers a breakthrough performance compared to `qi::alternative`.  
  
```  
///////////////////////////////////////////////////////////////////////////  
// Benchmark  
//   nabialek_trick_org: nabialek trick with rules  
//   nabialek_trick_ptr: nabialek trick with pointers to rules  
//  
// In both cases the nabialek trick is defined as a rule contained by  
// a container struct.  
///////////////////////////////////////////////////////////////////////////  
  
nabialek_trick_org:   2.3692825117 [s] {checksum: 0}  
nabialek_trick_ptr:   0.6684852074 [s] {checksum: 0}  
  
///////////////////////////////////////////////////////////////////////////  
// Benchmark  
//    qi alternative      : alternative  
//    qi::repo::kwd       : keyword  
//    nabialek operator   : nabialek  
//  
// In this sequence the tested expression is written explicitly to  
// the phrase_parse statement.  
// Because kwd and alternative must create their symbol table upon execution,  
// the nabialek operator direct test also creates the symbol table while  
// executing to be comparable.  
// An additional direct test (_dd) shows the nabialek operator with  
// predefined symbol table (which is the more common use case).  
///////////////////////////////////////////////////////////////////////////  
  
alternative_d:        0.6003579210 [s] {checksum: 0}  
keyword_d:            1.5455173284 [s] {checksum: 0}  
nabialek_d:           1.4926318310 [s] {checksum: 0}  
nabialek_dd:          0.5444593875 [s] {checksum: 0}  
  
///////////////////////////////////////////////////////////////////////////  
// Benchmark  
//    qi alternative      : alternative  
//    qi::repo::kwd       : keyword  
//    nabialek operator   : nabialek  
//  
// In this sequence the tested expression is taken from the container.  
// The rule explicitly defines the whole expression.  
///////////////////////////////////////////////////////////////////////////  
  
alternative_r:        5.3584585023 [s] {checksum: 0}  
keyword_r:            6.1443965268 [s] {checksum: 0}  
nabialek_r:           6.0566746704 [s] {checksum: 0}  
  
///////////////////////////////////////////////////////////////////////////  
// Benchmark  
//    qi alternative      : alternative  
//    qi::repo::kwd       : keyword  
//    nabialek operator   : nabialek  
//  
// In this sequence the tested expression is written to  
// the phrase_parse statement, but the conditional parsers are taken  
// from the container.  
// Because kwd and alternative must create their symbol table upon execution,  
// the nabialek operator direct test also creates the symbol table while  
// executing to be comparable.  
// An additional direct test (_rrr) shows the nabialek operator with  
// predefined symbol table (which is the more common use case).  
///////////////////////////////////////////////////////////////////////////  
  
alternative_rr:       0.6582615817 [s] {checksum: 0}  
keyword_rr:           1.6068278665 [s] {checksum: 0}  
nabialek_rr:          1.5839482918 [s] {checksum: 0}  
nabialek_rrr:         0.6539844901 [s] {checksum: 0}  
  
///////////////////////////////////////////////////////////////////////////  
// Benchmark  
//    qi alternative      : alternative  
//    qi::repo::kwd       : keyword  
//    nabialek operator   : nabialek  
//  
// In this sequence the tested expression is taken from rule container.  
// The rule references other rules of the container for the conditional  
// parsers.  
///////////////////////////////////////////////////////////////////////////  
  
alternative_ni:       6.7747060622 [s] {checksum: 0}  
keyword_ni:           7.0165952179 [s] {checksum: 0}  
nabialek_ni:          7.1159031090 [s] {checksum: 0}  
```  
  
Difficult to understand. The "long runners" in particular.  
  
There may be issues with the test cases I am not aware of. If you see some, please, tell me. I am a noob with this benchmarking suite.  
  
BTW, what about a `benchmark` directive to make a quick speed test of a rule or what available to anyone?

---

## Comment 51

> Username: mxc-commons  
> Created_at: 2016-08-15 17:49:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/200#issuecomment-239874176  

While preparing a benchmark for an arbitrary large number of keywords I found out that this n-ary approach actually is limited to n < 10. Proto max arity or what. Ok. Closed.

---
