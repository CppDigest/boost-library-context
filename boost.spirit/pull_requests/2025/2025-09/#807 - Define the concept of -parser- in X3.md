# #807 Define the concept of "parser" in X3 [Merged]

> Username: saki7  
> Created at: 2025-09-03 21:14:28 UTC  
> Updated at: 2025-09-25 00:13:02 UTC  
> Merged at: 2025-09-04 15:59:29 UTC  
> Closed at: 2025-09-04 15:59:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/807  

This PR defines the concept of "parser" in X3. This is essentially the starting point for a complete C++23-style overhaul of X3, enabling proper constraints and significantly improved compile times in derived parsers.  
  
I have experimented with this "modernization" attempt for 4 months and I strongly believe this PR is the requirement for updating X3 to C++23-ish codebase. I have already modernized most of the X3 codebase in my private fork, and it consists of 145 changed files, 15,000 additions, and 13,000 deletions.  
  
This PR is intentionally limited to include only the fundamental parts. Almost all core components need overhaul based on this PR, and I am going to submit dozens of follow-up PRs later.  
  
- partially related to boostorg/spirit_x4#14  
- partially related to boostorg/spirit_x4#16, but the documentation changes are not trivial (believe me, I tried) and needs follow-up PR.  
  
## Defining the "parser" concept  
  
The most fundamental definitions are described below. More detailed description can be found in the code comments.   
  
This definition is carefully designed based on my experience on self-implementing the bleeding edge (C++23 and later) features of the STL itself. (I have expert knowledge on how the standard library is implemented, and I occasionally submit LWG issues.)  
  
- **`x3::X3Subject<T>`:** `T` models an iterator-agnostic parser type; meaning that `T` is castable (by `x3::as_parser(T&&)`) to some well-defined parser class that inherits from `x3::detail::parser_base`.  
- **`x3::X3Parser<Parser, It, Se>`:** `Parser` models `X3Subject` and the member function `.parse(...)` is callable for given `It` and `Se`, with remaining parameters being `unused_type`.  
  
Above two concepts enable X3 to properly constrain many functions. For instance, the sequence operator (`>>`) was never constrained correctly, exposing unconstrained `typename Left` and `typename Right` on the public API. This sort of unconstrained features are considered bad design in C++23+ era and cause issues on library users.  
  
## Refine `x3::as_parser`  
  
`x3::as_parser` is now a C++23-style CPO. It prevents ADL in the first place, and provides the same semantics.  
  
The ADL customization point `as_spirit_parser` is removed because (1) based on a GitHub full-text search I couldn't find any use case except for the blog post by the person who initially implemented it in Spirit, and (2) it unnecessarily complicates the implementation details.   
  
More background info available in `parser.hpp`.  
  
## Modernization on the entry points  
  
`x3::parse(...)` and `x3::phrase_parse(...)` have been modernized accordingly. This includes the range support.  
  
## Overhaul on `x3::any_parser`  
  
This PR includes overhaul on `any_parser` as it operates on iterator/sentinel now. I can't split this change into a separate PR because the `any_parser` is tightly coupled with iterators and become completely broken without these changes.  
  
## Deprecation of `x3::any_parser`  
  
Furthermore, this PR deprecates `x3::any_parser`.   
  
I strongly believe that the system of "type-erased parser" in Spirit is historically abused and led to people creating overly generic parser definitions. Academically saying, PEGs are unambiguous, deterministic and always produce static grammar definition which operates on ordered choices (left to right). There's no need to dynamically create a RHS parser that depends on the left hand side of the _runtime_ input.  
  
During 10+ years of my experience in using Spirit, I myself have also used `any_parser` in the past. But now I admit that 100% of my usage was due to the misunderstanding of how PEG is defined in formal manner, and I indeed relied on `any_parser` to _bypass_ the statically-typed nature of PEG.  
  
A GitHub full-text search also proves my opinion, as the use case is limited to only few repositories. I've manually confirmed that every existing use case was involving the misunderstanding described above, and I strongly believe they can be rewritten without requiring complete type-erasure.  
  
Another notable example is boostorg/spirit#838, which initially demanded adding support for "lazy". However, after the long discussion, the thread has concluded with the fact that [the solution](https://github.com/boostorg/spirit/issues/838) does NOT require neither "lazy" nor "any_parser" functionality.   
  
The reasoning is also described in `any_parser.hpp`.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-03 22:37:50 UTC  
> Updated_at: 2025-09-03 22:40:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3251027456  

Please ignore the build failures on AppVeyor. The workflow configuration for Windows is currently poorly implemented and requires non-trivial changes to enable C++23 support in X3. I'll send a follow-up PR for that, and it's definitely off-topic.  
  
I'm developing this PR on Windows, and running tests locally every time before I push commits. The tests are passing.

---

## Comment 2

> Username: djowel  
> Created_at: 2025-09-04 00:06:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3251175250  

Go for it!

---

## Comment 3

> Username: djowel  
> Created_at: 2025-09-04 01:24:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3251345470  

Did a quick review. Looks good to me.

---

## Comment 4

> Username: saki7  
> Created_at: 2025-09-04 15:58:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3254403976  

Thanks for your review! I'll immediately start working on the Windows CI fix, then continue on the follow-up PRs.  
  
Note that the CI is failing for purely operational reasons (causes no harm to end users even if merged with "failing" status), and the tests are 100% passing in my local Windows machine. The workflow fix requires non-trivial changes, and I'm merging this PR now to avoid unnecessary rebase nightmare.

---

## Comment 5

> Username: saki7  
> Created_at: 2025-09-05 04:52:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3257060063  

CI is fixed in boostorg/spirit#808.

---

## Comment 6

> Username: djowel  
> Created_at: 2025-09-11 19:09:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282301041  

@saki7 There's been some backlash with this PR, mostly due to breaking changes. We should probably ease up and rethink what we are doing here. Boost thrives on consensus, and the discussions seem to be going in a negative direction, which should be addressed both here and in the mailing list. I'll urge the parties to raise the issues here.

---

## Comment 7

> Username: saki7  
> Created_at: 2025-09-11 19:12:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282309904  

@djowel Could you post your opinion on the mailing list? Most of the discussions are occurring on the mailing list and people are accusing me of these changes on the mailing list.

---

## Comment 8

> Username: djowel  
> Created_at: 2025-09-11 19:31:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282367311  

> @djowel Could you post your opinion on the mailing list? Most of the discussions are occurring on the mailing list and people are accusing me of these changes on the mailing list.  
  
I wrote a reply. I am urging people to have the technical discussions done here as well. The problem is communication. Discussions are happening everywhere. I don't even see the affected parties in the list, actually, esp. the MQTT authors.

---

## Comment 9

> Username: saki7  
> Created_at: 2025-09-11 19:38:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282386254  

FYI, the MQTT authors have already decided to use more stable alternative.  
  
https://github.com/boostorg/mqtt5/issues/36#issuecomment-3277786957  
  
> In particular, MQTT5 is typically used not in hobby projects but in systems running on small, resource-constrained devices with custom SoCs—hardware designed to operate for years. It’s unrealistic to expect the toolchains for these devices to be bleeding-edge at all times. We still want to support developers targeting such platforms. Our goal is to provide a useful library that works with minimal effort. Their needs and projects matter most, not ours.  
  
I respect their choice and I'm not pushing X3 toward acceptance by such target-specific frameworks.

---

## Comment 10

> Username: djowel  
> Created_at: 2025-09-11 19:53:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282426367  

> FYI, the MQTT authors have already decided to use more stable alternative.  
>   
> [boostorg/mqtt5#36 (comment)](https://github.com/boostorg/mqtt5/issues/36#issuecomment-3277786957)  
>   
> > In particular, MQTT5 is typically used not in hobby projects but in systems running on small, resource-constrained devices with custom SoCs—hardware designed to operate for years. It’s unrealistic to expect the toolchains for these devices to be bleeding-edge at all times. We still want to support developers targeting such platforms. Our goal is to provide a useful library that works with minimal effort. Their needs and projects matter most, not ours.  
>   
> I respect their choice and I'm not pushing X3 toward acceptance by such target-specific frameworks.  
  
I am not really sure how many other libraries depend on Spirit X3. I know I have a LOT that was written many years ago and would require porting if I need to bring it up to date. But it's a balance, and X3 was really originally conceptualized as e**X**perimental and pushing the boundaries of c++ (at the time C++14). I'd prefer to keep that original objective. But at the same time, I also do not want to alienate users of X3. The problem is the lack of communication. It used to be that there was just the Boost dev and users list. Now, communication is VERY fragmented.

---

## Comment 11

> Username: saki7  
> Created_at: 2025-09-11 20:08:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282468885  

> The problem is the lack of communication. It used to be that there was just the Boost dev and users list. Now, communication is VERY fragmented.  
  
I truly agree with this concern. This is the reason why I've never submitted any single mail to the list, despite that I'm the heavy reader who has subscribed on the list for more than 10 years.  
  
The problem is that the all political discussions occur on the Boost Developers Mailing List, and the core devs are almost always making important decisions on the list. Whenever a core dev posts some personal thoughts on the mailing list, if we don't reply to them, we lose the chance to provide information to correct misunderstandings.

---

## Comment 12

> Username: Lastique  
> Created_at: 2025-09-11 23:05:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282911347  

> X3 was really originally conceptualized as e**X**perimental and pushing the boundaries of c++ (at the time C++14). I'd prefer to keep that original objective.  
  
This contradicts the statement [here](https://github.com/boostorg/spirit_x4/issues/15), specifically that X3 is currently the official, main version of Spirit.  
  
Unless you're saying that Spirit as a whole is an experimental library not suitable for production usage, I think you (Spirit maintainers) need to make a decision and announce which versions of Spirit are actively maintained and serve which purposes. And in particular, which version of Spirit should be considered stable and production-ready by users. This announcement should probably be made not here but on Boost ML and Spirit release notes, and the same should be clear from the documentation.

---

## Comment 13

> Username: saki7  
> Created_at: 2025-09-11 23:19:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282938084  

My understanding is that the spirit of Spirit is "eXperimental", while the current library status for X3 is indeed the main version. These two facts does not contradict with each other.   
  
An enthusiastic, "eXperimental" library which pushes the boundaries of C++ can technically be the main version at the same time.  
  
V2 (Qi & Karma & Lex) is _in fact_ not actively maintained thus we can't say they are the "main" version.

---

## Comment 14

> Username: djowel  
> Created_at: 2025-09-11 23:33:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3282965760  

> > X3 was really originally conceptualized as e**X**perimental and pushing the boundaries of c++ (at the time C++14). I'd prefer to keep that original objective.  
>   
> This contradicts the statement [here](https://github.com/boostorg/spirit_x4/issues/15), specifically that X3 is currently the official, main version of Spirit.  
>   
> Unless you're saying that Spirit as a whole is an experimental library not suitable for production usage, I think you (Spirit maintainers) need to make a decision and announce which versions of Spirit are actively maintained and serve which purposes. And in particular, which version of Spirit should be considered stable and production-ready by users. This announcement should probably be made not here but on Boost ML and Spirit release notes, and the same should be clear from the documentation.  
  
Very good point, and this needs some elaboration. What I am saying is that originally, rewind 10-15 years, there was Qi and Karma, which were the main libraries of Spirit. X3 was the experimental branch at the time. X3 pushed into C++14. It has since matured and became stable. Yes, it was originally conceptualized as eXperimental and pushing the boundaries of c++. Now, fast forward, 2025, whether or not it should keep the "X" brand is subject to discussion and debate, but, historically, Spirit, as a whole, always pushed the boundaries of C++. Always, from the very start.  
  
This discussion is somewhat nostalgic, in the sense that, this was also the situation when X3 came to be: there were mature libraries (Qi, Karma) that need to be maintain backward compatibility and not introduce breakage because there are dependent libraries, and we need to be very careful. But that also prevented me from exploring new C++ features (C++14 at the time and onwards to C++17).   
  
I simply overlooked the fact that X3 already has dependent libraries and users, and I regret that.  
  
For that matter, the suggestion to make X3 stable and move to X4 is starting to be appealing. But that, of course, depends on @saki7.

---

## Comment 15

> Username: jpyllman  
> Created_at: 2025-09-12 15:13:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3285679179  

I really think this new effort from @saki7 should be x4, or v4. It is kind of hard with breaking ABI and other dependencies when you have others depending on you.  
  
But also, to do this there has to be some smart documentation that will explain that classic and v2 is not maintained and that x3/v3 will maybe be bugfix only and v4 will be the new world with c++23 and forward version required.  
  
But also, in one way, have anybody thought about what the new c++26 things can bring to the mix?? Maybe v4 should be c++26. And we in smart ways keep ABI but 'sprinkle' some C++23 magic if compiler support it for v3?? Just my thoughts.

---

## Comment 16

> Username: saki7  
> Created_at: 2025-09-12 21:11:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3286830902  

@jpyllman I value your opinion. Actually, the recent suggestion I just posted on the mailing list is heading to the same way as you mentioned.  
  
Regarding the C++26 support: there were two reasons that I chose C++23 for the initial "modernization" attempt.  
  
(1) To make sure the change is somewhat less _aggressive_.   
(2) The three major toolchains, GCC/Clang/MSVC, currently does not have interoperable baseline support on C++26 features. For instance, if we use `std::text_encoding` in Spirit, that would instantly break Clang and MSVC.  
  
I personally use `/std:c++latest` in 100% of my new applications. I'm the person who has the belief of "why not just try out the most recent language anyway?". That said, we should consider more carefully about the requirement in Spirit, even if it's going to be Spirit.X4. According to the points mentioned in (2), I still don't think C++26 is feasible (yet).  
  
Don't worry. I will officially bump the version (of Spirit.X4) to C++26 as soon as we feel it is appropriate. Now is not the time.

---

## Comment 17

> Username: jpyllman  
> Created_at: 2025-09-12 21:25:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3286871927  

@saki7, when the first discussion about splitting different version and what to name them I did not want to put even more confusion to the discussion. But when this talk about compiler support and to different ABI I just felt I needed to say something.  
  
I am very happy you are putting all this effort into spirit. There are great things in newer version of C++ to help Spirit. But at the same time. I feel like the already existing code already using spirit might not handle to big changes. Some things like constexpr and noexcep and maybe conditionally have concepts is possible. And then I thought, if you anyway is going to break things, maybe already now look at what could be great from C++26 and what design implications that would bring. And that brings a new major version that does not need to support older versions of C++.

---

## Comment 18

> Username: saki7  
> Created_at: 2025-09-12 21:34:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3286916603  

> And then I thought, if you anyway is going to break things, maybe already now look at what could be great from C++26 and what design implications that would bring. And that brings a new major version that does not need to support older versions of C++.  
  
This can be considered as one of the valid directions for _applications_. However we're building _library_ here, and we should be conservative at the least level like I mentioned. This applies to Spirit.X4, despite that it is indeed going to be a brand new library.  
  
We should also note that all of GCC/Clang/MSVC tend to emit false-positive errors on bleeding edge features.  
  
I use all three major compilers, GCC/Clang/MSVC, for my applications. I use MSVC to develop game. I use GCC/Clang to test whether my code is conforming to the standard. For this purpose I really need 3 toolchains to make sure I'm writing legit code.

---

## Comment 19

> Username: saki7  
> Created_at: 2025-09-13 03:51:34 UTC  
> Updated_at: 2025-09-13 03:51:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3287504916  

The Boost developers has reached a consensus that new development will take place on [Spirit.X4](https://github.com/boostorg/spirit_x4).  
  
X3 is reverted to the C++17 baseline, officially feature-frozen, and will accept security patches only.  
  
For details, see: <https://lists.boost.org/archives/list/boost@lists.boost.org/thread/K3EQLEQJHEUBROB6ODUKTZHFP2FDUS2E/?sort=date>

---

## Comment 20

> Username: Xeverous  
> Created_at: 2025-09-24 14:56:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329094301  

Hello, I have come to Spirit's repository to report a suspected regression bug. Some of the users here might remember me for reporting multiple bugs few years ago and other collaboration - Joel especially although I departed from Elements library in pursuit of something different in a quite ungraceful way for which I apologize.  
  
I just read all the things about X3/X4 in the project's readme and it left me somewhat sad and confused.  
  
- Since X4 is not yet an official part of Boost, does it mean the current Boost has no maintained Spirit library?  
- If I understood right, can X3 still be maintained? I saw mentions of reverting PR. C++17 to C++23/26 is a huge jump and I want X3 to remain supported. I could become a maintainer of X3 but since Boost.Parser exists and all the people from X3 moved to X4, should I consider moving my project from X3 to Boost.Parser instead?  
- Is Boost.Parser supposed to be an alternative to (allegedly stale) X3? Why are there so many parser libraries in Boost?  
- What's the/Is there consensus regarding tools of communication? I saw somewhat long discussion on the linked mailing list with no clear answer. Personally I would prefer something other/more instant than mails e.g. Slack or Github Discussions (and also don't want to expose private email address).

---

## Comment 21

> Username: Lastique  
> Created_at: 2025-09-24 15:57:26 UTC  
> Updated_at: 2025-09-24 15:59:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329634826  

> * Is Boost.Parser supposed to be an alternative to (allegedly stale) X3?  
  
Boost.Parser and Boost.Spirit and different, unrelated libraries. You can use whichever suits your needs best.  
  
> * What's the/Is there consensus regarding tools of communication?  
  
There are several channels of communication, including Slack. See [here](https://www.boost.org/community/). Though I'm not sure if Boost.Spirit or Boost.Parser maintainers are active on Slack.  
  
In general, official and community-wide discussions are better held on the devs mailing list. Discussions pertaining to specific issues and PRs are typically in the issues and PR comments. Slack is for more interactive communication.  
  
BTW, I think you can use a forum-like web interface to the mailing list. See [here](https://lists.boost.org/archives/list/boost@lists.boost.org/).

---

## Comment 22

> Username: saki7  
> Created_at: 2025-09-24 16:08:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329702842  

Thank you for your comment, I'll reply to your points one by one.  
  
> Since X4 is not yet an official part of Boost, does it mean the current Boost has no maintained Spirit library?  
  
X4 is official part of the Boost.  
  
It's just not added to the `boostorg/boost` superproject yet, due to temporal reasons. Please pick up the nuance in the README bit more carefully: [link](https://github.com/boostorg/spirit_x4/blob/2863465a8bb4ad69019e108a268cce41855bc3a4/README.md?plain=1#L5). I've gone through the very, very tough discussion on the mailing list to reach the consensus.   
  
> ... does it mean the current Boost has no maintained Spirit library?  
  
Spirit.Classic, Spirit.V2, and Spirit.X3 are unmaintained. Spirit.X4 is maintained.  
  
> If I understood right, can X3 still be maintained? I saw mentions of reverting PR. C++17 to C++23/26 is a huge jump and I want X3 to remain supported. I could become a maintainer of X3 but since Boost.Parser exists and all the people from X3 moved to X4, should I consider moving my project from X3 to Boost.Parser instead?  
  
X3 _cannot_ be maintained, for both political and technical reasons.  
  
Before the modernization work got reverted, I was the primary maintainer of X3. I decided to choose the _hard_ path because I assumed that maintaining X3 while retaining the C++17 codebase would introduce serious maintenance burden, and even I, as the primary maintainer, can't afford my time for doing such unconstructive work.  
  
As of now, X4 has a significantly diverged (better) codebase which cannot interoperate with the X3 codebase. If we are to have an independent maintainer for X3, that would make the Boost Libraries to have two independent Spirits, which is very much unacceptable. I believe that you can agree with this point, as it is **even worse** than the situation you mentioned, given that we're going to have _three_ different parser libraries (Spirit.X3 & Spirit.X4 & Boost.Parser) in the Boost Libraries.  
  
Even if someone volunteers to _maintain_ X3, that would ultimately make the community divide into two factions. Then it can cause more and more drama over time, and it will cause pain on both the Spirit developers and the end users. This is why I stated that Spirit.V2 and Spirit.X3 are feature-frozen and only accepts security patches.  
  
> I want X3 to remain supported. I could become a maintainer of X3  
  
No offense, but I must admit that saying this is easy. I know there are many friendly users who are supporting Spirit. But maintaining a library is not a trivial task. I truly appreciate your past bug reports, but there is considerable distance between bug reports and maintenance work.   
  
> Is Boost.Parser supposed to be an alternative to (allegedly stale) X3? Why are there so many parser libraries in Boost?  
  
No. Boost.Parser differs from X3 in very significant ways. You may consider them as two independent libraries. It's the user who choose which one to use, not us.  
  
You should take a look at its codebase for couple of hours, and I'm sure you'll understand what I mean (really, look at its mega header file that consists of 9,790 lines). They even claim that X3 has some traits that are ["quite difficult to get working", "suffer from limitations", "deal-breaker", "could see no way to fix it"](https://www.boost.org/doc/libs/1_89_0/doc/html/boost_parser/this_library_s_relationship_to_boost_spirit.html#boost_parser.this_library_s_relationship_to_boost_spirit.the_spirit_x3_rule_problem). I consider that statement a very hostile attitude coming from its maintainer, and I don't think we can have constructive discussion on this matter.  
  
If you insist on continuing this theme, please understand that I must lock the conversation as off-topic, if the argument goes to the undesired direction.  
  
> What's the/Is there consensus regarding tools of communication? I saw somewhat long discussion on the linked mailing list with no clear answer. Personally I would prefer something other/more instant than mails e.g. Slack or Github Discussions (and also don't want to expose private email address).  
  
The official place for building organization-wide consensus is the Boost Developers Mailing List. I personally agree that email is not the best place for daily conversations, but I think the mailing list is still the good place for building important consensus.

---

## Comment 23

> Username: Lastique  
> Created_at: 2025-09-24 16:24:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329767189  

> As of now, X4 has a significantly diverged (better) codebase which cannot interoperate with the X3 codebase.  
  
Do I understand it correctly that X3 and X4 cannot be both used in the same program, including when X3 and X4 don't inter-opereate? If so, this is a problem because there may be different parts of the program (e.g. different libraries) that independently use X3 and X4, and IMO that should not cause issues to the user.  
  
> If we are to have an independent maintainer for X3, that would make the Boost Libraries to have two independent Spirits, which is very much unacceptable. I believe that you can agree with this point, as it is **even worse** than the situation you mentioned, given that we're going to have _three_ different parser libraries (Spirit.X3 & Spirit.X4 & Boost.Parser) in the Boost Libraries.  
  
I see nothing wrong with X3 (and v2 for that matter) being maintained while X4 is under active development. In fact, I would view that as the preferred state of things. If everything pre-X4 is unsupported (including no bug fixes) then Spirit as a whole is effectively unsuitable for production code bases. Because X4 is permanently the bleeding edge/experimental and therefore is unsuitable by definition. I realize this would require more efforts on the maintainers, but that's just how I see it.

---

## Comment 24

> Username: saki7  
> Created_at: 2025-09-24 16:34:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329798236  

> Do I understand it correctly that X3 and X4 cannot be both used in the same program, including when X3 and X4 don't inter-opereate? If so, this is a problem because there may be different parts of the program (e.g. different libraries) that independently use X3 and X4, and IMO that should not cause issues to the user.  
  
Don't worry, X3 and X4 are usable in the same application. X3's headers are inside `boost/spirit/home/x3/`, while X4 headers are inside `boost/spirit/x4/`. All include guards and public macros are named differently. What I meant by "not interoperable" is that, user-defined parser class that inherits from `x3::parser` cannot be used with `x4::parser`. I also don't expect backports to happen, as the implementation detail is very, very different.  
  
> Because X4 is permanently the bleeding edge/experimental and therefore is unsuitable by definition. I realize this would require more efforts on the maintainers, but that's just how I see it.  
  
If I understand your comment correctly, your comment is based on your point of view, which itself is a legitimate opinion. The problem is, your thoughts do not empathize with the facts that Joel mentioned: ["Spirit, as a whole, always pushed the boundaries of C++. Always, from the very start."](https://github.com/boostorg/spirit/pull/807#issuecomment-3282965760).  
  
If you can't agree with this policy, it's okay. We're not even trying to change your opinion. It's just that we (Spirit maintainers) must choose some direction based on our own policy, and that's the outcome we have had in the mailing list.

---

## Comment 25

> Username: Lastique  
> Created_at: 2025-09-24 16:47:18 UTC  
> Updated_at: 2025-09-24 16:47:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329833691  

"Pushing the boundaries of C++" doesn't mean leaving previous (stable) versions unsupported. That's the purpose of bumping the versions in the first place - to branch off the bleeding edge version from the previous stable version that gained external users in order to avoid breaking them. I don't have a problem with branching off the bleeding edge version, quite the contrary. I do have a problem with leaving stable versions, with existing users, purposely unsupported, including *without bug fixes*.

---

## Comment 26

> Username: saki7  
> Created_at: 2025-09-24 16:50:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329843925  

> I do have a problem with leaving stable versions, with existing users, purposely unsupported, including without bug fixes.  
  
If you have bug fixes then just go ahead and submit the PR right now. I promise to merge it, as long as it does not change the existing semantics and does not add any new features.

---

## Comment 27

> Username: Lastique  
> Created_at: 2025-09-24 16:56:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329865617  

> If you have bug fixes then just go ahead and submit the PR right now.  
  
So bug reports for pre-X4 won't be closed as "unsupported" and bug fixes will be accepted going forward, correct? If so, that's good to hear. Please, update the readme accordingly (it currently says "security patches only"). Thanks.

---

## Comment 28

> Username: saki7  
> Created_at: 2025-09-24 17:01:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329879757  

> > >  I do have a problem with leaving stable versions, with existing users, purposely unsupported, including without bug fixes.  
> >  
> > If you have bug fixes then just go ahead and submit the PR right now. I promise to merge it, as long as it does not change the existing semantics and does not add any new features.  
>  
> So bug reports for pre-X4 won't be closed as "unsupported" and bug fixes will be accepted going forward, correct? If so, that's good to hear. Please, update the readme accordingly (it currently says "security patches only"). Thanks.  
  
Sigh. Bug fixes that "does not change the existing semantics and does not add any new features" is technically a security patch. There's no margin between the security patch and the "bug fixes" I mentioned.  
  
Seriously, we're building a parser combinator library. If there exists some "bug fixes" that does not change the semantics of the parser combinator library, that is strictly equal to security patches.  
  
The Boost Organization have a very strong consensus that sublibraries decide what to maintain. We (Spirit maintainers) decide that we make the obsolete versions feature-frozen. That's the end of the story.

---

## Comment 29

> Username: Lastique  
> Created_at: 2025-09-24 17:21:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329944506  

There's a wide range of potential problems that are not security issues. From compiler warnings (including, from yet to be released compilers) to compilation errors to runtime misbehavior (i.e. behavior not according to the documentation).  
  
Of course, you can decide to maintain whatever you want. But that has a consequence for your users, existing and potential, and the responsible thing to do is to tell your users up-front whether they are supported (and whether you want users at all). For example, I have used Spirit in a few places, and right now I'm deciding what to do with that code and what the support status is. If you're saying that v2 and X3 are unsupported, I'll probably have to rewrite that code and not bother with Spirit again because of this support policy. Please, understand me correctly, this isn't a threat or anything, this is me trying to clarify things for myself and other users.

---

## Comment 30

> Username: saki7  
> Created_at: 2025-09-24 17:28:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3329964236  

> If you're saying that v2 and X3 are unsupported, I'll probably have to rewrite that code and not bother with Spirit again because of this support policy.   
  
V2 and X3 are **NOT** unsupported, they are technically supported, as we are accepting security patches. They are just "not actively maintained" and "feature-frozen".  
  
If you can't cope with Spirit's policy, which existed from the very beginning of its introduction, just go ahead and make your own decisions on your own application. There's nothing, absolutely nothing, that we can do about your personal work.

---

## Comment 31

> Username: saki7  
> Created_at: 2025-09-24 17:48:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3330029619  

> compiler warnings  
  
Compiler warnings are not bugs. They are _messages_ that imply the existence of potential mistakes. If that mistake has the risk of uninitialized memory access, for instance, then it is technically a security bug. We accept patches that resolves the security bug, which might resolve the warnings as a consequence.  
  
> runtime misbehavior (i.e. behavior not according to the documentation).  
  
Define "misbehavior". We define the "behavior" as the status quo of the Spirit library. That is exactly the "existing semantics" I mentioned. If we even try to change a single meaning, that would break someone else's assumptions, which requires the _full_ maintenance work to deploy. We don't accept such changes, because V2 and X3 are feature-frozen.

---

## Comment 32

> Username: Xeverous  
> Created_at: 2025-09-24 19:58:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3330468064  

Thanks you both for a fast response.  
  
> BTW, I think you can use a forum-like web interface to the mailing list. See here.  
  
This looks attractive to me, but if I create an account and post through the forum, will my private email be exposed?  
  
> If we are to have an independent maintainer for X3, that would make the Boost Libraries to have two independent Spirits, which is very much unacceptable. [...] Even if someone volunteers to maintain X3, that would ultimately make the community divide into two factions.  
  
Yes, I agree with this point. I don't want to be a maintainer in such case and don't want to fragment the community.  
  
> Boost.Parser differs from X3 in very significant ways. You may consider them as two independent libraries. It's the user who choose which one to use, not us.  
  
I have read significant part of Boost.Parser documentation and it looks almost exactly like X3. Nearly every function has the same name and even the meanings of operator overloads are the same. I looks very much like someone forked X3 or wants to support X3 users.  
  
> You should take a look at its codebase for couple of hours, and I'm sure you'll understand what I mean (really, look at its mega header file that consists of 9,790 lines). They even claim that X3 has some traits that are "quite difficult to get working", "suffer from limitations", "deal-breaker", "could see no way to fix it". I consider that statement a very hostile attitude coming from its maintainer, and I don't think we can have constructive discussion on this matter.  
  
I did had some bad experience with Spirit X3, exactly the points Boost.Parser mentions as problematic or "deal-breaker". X3 has complicated attribute rules and tuple/container matching, with lots of surprises like requiring intermediate grammars in some cases to break dependencies/complexity or disallowing tuples of size 1. I reported multiple issues (most were real bugs) but personally I still found documentation lacking in this area, attributes were the hardest part to get right from my (user's) perspective.  
  
C++23 is too big requirement for me and Boost.Parser (which requires C++17, optionally C++20) looks very similar - a potential viable alternative.  
  
> No. Boost.Parser differs from X3 in very significant ways.  
  
What ways? You mean the attribute design or something else? The library's interface looks extremely similar and I predict I could change the namespace and ~80-90% of my code would already be compilable.  
  
If Boost is going to have 2 parsing libraries (X4 and Parser) I think it is in the interest of Boost to inform Boost users about library differences. Spirit (any version) is a complex idea and I think it is a great value to users if library authors explain fundamental design decisions and key differences from other projects.  
  
There might be more users of X3 who are in the same position as me and C++23 can also be too much of a requirement for  them. I think X3 should inform about Boost.Parser as another potential alternative library to X3. I hope you don't view Boost.Parser as hostile competition.  
  
> Bug fixes that "does not change the existing semantics and does not add any new features" is technically a security patch. [...] If there exists some "bug fixes" that does not change the semantics of the parser combinator library, that is strictly equal to security patches.  
  
That's not a definition I had in mind and @Lastique seems to also be confused. I think lots of users understand "security patch" only as something that fixes a crash or security exploit, not ordinary "implementation has a mistake" bug. I would edit Readme for clarification.

---

## Comment 33

> Username: Lastique  
> Created_at: 2025-09-24 20:04:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3330491283  

> > BTW, I think you can use a forum-like web interface to the mailing list. See here.  
>   
> This looks attractive to me, but if I create an account and post through the forum, will my private email be exposed?  
  
I'm not sure, but probably yes, the email address that you use to subscribe to the mailing list will be present in the post, when received by recipients.

---

## Comment 34

> Username: djowel  
> Created_at: 2025-09-24 20:06:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3330499512  

I think there's a big misunderstanding here regarding maintenance and feature freeze. These are not mutually exclusive. While a library, e.g. X3, V2 and even Classic, along with the infrastructure libraries such as fusion, can be feature frozen, with no new features planned, there can always be some basic level of maintenance, like say, ensuring that the tests are running fine (basically github admin tasks) and that the code base is still keeping up with any potential breaking changes to the boost core (that could be very trivial like macro name changes, for example), clearing up or accepting (or not accepting) trivial non-breaking PRs such as documentation tweaks (seldom, but possible), or even keeping up with new compiler updates (e.g. even if we lock the code to say C++ 17, there may still be potential breaking changes that may cause some minor changes, again very seldom or not at all, but it can happen), etc.  
  
I'm with @Lastique here. V2 and X3 can be feature-frozen, but still maintained, with some basic level of maintenance—just enough to keep things running smoothly.  
  
> > compiler warnings  
>   
> Compiler warnings are not bugs. They are _messages_ that imply the existence of potential mistakes. If that mistake has the risk of uninitialized memory access, for instance, then it is technically a security bug. We accept patches that resolves the security bug, which might resolve the warnings as a consequence.  
  
Sure, compiler warnings are not bugs, but these are still technically maintenance tasks that need to be dealt with. Even the mere act of disabling a compiler warning is maintenance. Compiler warnings need not necessarily imply the existence of potential mistakes. It can simply be, for example, a new compiler version adding a compiler warning that was not there before. It can happen.

---

## Comment 35

> Username: Xeverous  
> Created_at: 2025-09-24 20:16:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3330545615  

> V2 and X3 can be feature-frozen, but still maintained, with some basic level of maintenance—just enough to keep things running smoothly.  
  
Speaking of maintenance, my initial reason for visiting this project's issues today was to report a potential regression. I will open a new issue before end of the week. I'm not sure if it is a bug (might be another case of misunderstanding parsers and attributes) but I would like someone to look into it because parser's output is very surprising to me and a full Boost update on my project caused some of my unit tests to fail.

---

## Comment 36

> Username: djowel  
> Created_at: 2025-09-24 20:37:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3330632724  

> > V2 and X3 can be feature-frozen, but still maintained, with some basic level of maintenance—just enough to keep things running smoothly.  
>   
> Speaking of maintenance, my initial reason for visiting this project's issues today was to report a potential regression. I will open a new issue before end of the week. I'm not sure if it is a bug (might be another case of misunderstanding parsers and attributes) but I would like someone to look into it because parser's output is very surprising to me and a full Boost update on my project caused some of my unit tests to fail.  
  
Not sure about this, but the mere act of handling such a github issue as "won't-fix" because we are in feature-freeze is maintenance.

---

## Comment 37

> Username: Xeverous  
> Created_at: 2025-09-24 20:49:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3330665533  

> Not sure about this, but the mere act of handling such a github issue as "won't-fix" because we are in feature-freeze is maintenance.  
  
True, this may happen. I won't blame the maintainers, C++ is hard and it takes time to verify or debug code, especially libraries as complex as Spirit. I just want the hours I spent on reproducing it to not go to waste. Maybe I discovered something big again, something that could help X4's development or something that maintainers will manage to fix very fast. I will try to git bisect Spirit's code and check if my issue is indeed a regression.

---

## Comment 38

> Username: djowel  
> Created_at: 2025-09-24 20:55:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3330683823  

> > Not sure about this, but the mere act of handling such a github issue as "won't-fix" because we are in feature-freeze is maintenance.  
>   
> True, this may happen. I won't blame the maintainers, C++ is hard and it takes time to verify or debug code, especially libraries as complex as Spirit. I just want the hours I spent on reproducing it to not go to waste. Maybe I discovered something big again, something that could help X4's development or something that maintainers will manage to fix very fast. I will try to git bisect Spirit's code and check if my issue is indeed a regression.  
  
We’ll see. But my point is that handling a GitHub issue is, in and of itself, a maintenance task.

---

## Comment 39

> Username: saki7  
> Created_at: 2025-09-25 00:13:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/807#issuecomment-3331136072  

Fixed in https://github.com/boostorg/spirit/commit/07c58b413d306d8ab3a246cc39de571f581ef545

---
