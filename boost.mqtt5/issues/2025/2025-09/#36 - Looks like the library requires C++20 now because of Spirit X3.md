# #36 - Looks like the library requires C++20 now because of Spirit X3 [Closed]

> Username: pdimov  
> Created at: 2025-09-09 00:32:43 UTC  
> Updated at: 2025-10-21 17:08:49 UTC  
> Closed at: 2025-09-19 09:33:54 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36  

```  
[ 81%] Building CXX object libs/mqtt5/test/CMakeFiles/boost_mqtt5-tests.dir/integration/async_sender.cpp.o  
In file included from /home/runner/work/boost/boost/libs/spirit/include/boost/spirit/home/x3/support/traits/has_attribute.hpp:12,  
                 from /home/runner/work/boost/boost/libs/spirit/include/boost/spirit/home/x3/core/parser.hpp:14,  
                 from /home/runner/work/boost/boost/libs/spirit/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:12,  
                 from /home/runner/work/boost/boost/libs/spirit/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /home/runner/work/boost/boost/libs/spirit/include/boost/spirit/home/x3.hpp:62,  
                 from /home/runner/work/boost/boost/libs/mqtt5/include/boost/mqtt5/impl/codecs/base_decoders.hpp:18,  
                 from /home/runner/work/boost/boost/libs/mqtt5/include/boost/mqtt5/impl/codecs/message_decoders.hpp:15,  
                 from /home/runner/work/boost/boost/libs/mqtt5/include/boost/mqtt5/impl/assemble_op.hpp:16,  
                 from /home/runner/work/boost/boost/libs/mqtt5/include/boost/mqtt5/impl/client_service.hpp:15,  
                 from /home/runner/work/boost/boost/libs/mqtt5/include/boost/mqtt5/mqtt_client.hpp:18,  
                 from /home/runner/work/boost/boost/libs/mqtt5/test/integration/async_sender.cpp:8:  
/home/runner/work/boost/boost/libs/spirit/include/boost/spirit/home/x3/support/traits/attribute_of.hpp:29:9: error: ‘requires’ does not name a type  
   29 |         requires requires {  
      |         ^~~~~~~~  
/home/runner/work/boost/boost/libs/spirit/include/boost/spirit/home/x3/support/traits/attribute_of.hpp:29:9: note: ‘requires’ only available with ‘-std=c++20’ or ‘-fconcepts’  
```

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-09-09 12:39:52 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3270548811  

Thanks for raising this!  
We'd prefer not to raise the requirement to C++20, so I'll explore other options.

---

## Comment 2

> Username: saki7  
> Created at: 2025-09-10 15:14:41 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3275423174  

Spirit maintainer here. Sorry about the inconvenience; we actually bumped the minimal required version to C++23.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2025-09-10 15:25:11 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3275464775  

And this is why I do not like to use bulky outside parsing libraries. I will talk to the mqtt author and offer them an alternative that requires only C++11.

---

## Comment 4

> Username: saki7  
> Created at: 2025-09-10 15:51:25 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3275557732  

Well I actually agree that it's bulky, since I'm doing a complete overhaul targeting C++23. But it's not "bulky" parsing library. It's "bulky bleeding-edge" parsing library.  
  
Thanks

---

## Comment 5

> Username: vinniefalco  
> Created at: 2025-09-10 17:35:30 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3275902115  

Why do you need C++23? What are you doing with it that can't be done using a lower language version?

---

## Comment 6

> Username: saki7  
> Created at: 2025-09-10 17:53:44 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3275961209  

That's a great question. I always wanted to have a technical discussion with you since I'm a big fan of you on the mailing list. (This is not an irony or something, I've been reading the ML for 15 years and I genuinely like your stance)  
  
Examples of such features that I already use in X3 are:  
  
- [`CWG2518`](https://cplusplus.github.io/CWG/issues/2518.html)  
- `static operator()` -- for CPO  
- `static operator[]` -- for CPO  
  
Examples that I plan to use in the future are:  
  
- `std::unreachable` -- for optiomization  
- `std::forward_like` -- for use with deducing this  
- `deducing this` -- for the CRTP base class  
- `#warning` -- for deprecating old features  
- `auto(x)` and `auto{x}` -- for parser attribute cloning

---

## Comment 7

> Username: vinniefalco  
> Created at: 2025-09-10 17:57:59 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3275973727  

it seems like most or all of those things can be done in lower language versions, albeit perhaps with less elegant syntax. still, this doesn't affect the public API (or am I mistaken?)   
  
There is also `BOOST_UNREACHABLE` which works in C++11

---

## Comment 8

> Username: saki7  
> Created at: 2025-09-10 18:02:57 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3275988485  

CWG2518 is the most notable example. It allows you to write `static_assert` on uninstantiated branch of `if constexpr`. This is very essential for X3, because Spirit has historically suffered from thousands of lines of compilation errors, which was the huge barrier for beginners. The ease of writing `static_assert` on unevaluated branch makes the code very short and allows friendly errors.  
  
Deducing this is an another example that cannot be done in lower versions. You simply can't, because it's core language feature.

---

## Comment 9

> Username: saki7  
> Created at: 2025-09-10 18:11:15 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276013868  

There's one political reason that I didn't mention: young people.  
  
Why would a high school student who likes to experiment with C++ consider some obsolete (C++17) library as attractive?  
Boost is dying and we're doomed if we don't use bleeding edge features to attract young people.  
  
You might think this is some kind of joke but I believe this is the right way to proceed.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2025-09-10 18:41:48 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276115918  

So you think "young people" are going to look at your implementation and conclude that the library better suits their particular needs because it uses C++23 language features which do not improve the library's public API? This is nonsense.

---

## Comment 11

> Username: saki7  
> Created at: 2025-09-10 18:46:53 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276130571  

No, I meant the other way. Young people will avoid contribution to the existing library if it sticks to the C++ standard that is now obsolete for 2 versions. That was my feeling when I saw a C++03 library when I was a high school student. I'm 30 years old now and still think the same.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2025-09-10 18:55:46 UTC  
> Updated at: 2025-09-10 18:58:38 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276155140  

Is your goal to create a useful library which enjoys a wide audience or is your goal to attract contributions from indiviudals who are not library authors and don't have users of their own? C++03 is a very different thing from C++17.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2025-09-10 18:57:23 UTC  
> Updated at: 2025-09-10 18:57:53 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276159549  

By comparison, Boost.Spirit:  
  
<img width="415" height="67" alt="Image" src="https://github.com/user-attachments/assets/1058deec-c75f-4a4f-a72e-baf2c4d26b6e" />  
  
And Boost.Beast which requires only C++11:  
  
<img width="453" height="76" alt="Image" src="https://github.com/user-attachments/assets/6599af7f-2f1e-426c-8093-0211dd735738" />  
  
Boost.Spirit is considerably older that Beast yet has one tenth of the stars.

---

## Comment 14

> Username: saki7  
> Created at: 2025-09-10 18:59:08 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276164760  

My goal is to revitalize C++. It is impossible without gathering contributions from the young people.  
  
Some people write C++ for business, while some people write C++ because it's genuinely fun to hack. That's the difference.  
  
It's the same reason that Rust is getting wider audience than C++.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2025-09-10 19:00:51 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276169255  

It seems there are diminishing returns on continuing the discussion, and also considering this is hijacking an issue in a different library. I will just leave you with one final thought. You are wrong about everything, in particular what young people want. In fact, we made a documentary actually asking young people what they thought about C++ and they refute all of your points above. Watch here:  
  
https://www.youtube.com/watch?v=HC9uNe4W6Tw

---

## Comment 16

> Username: saki7  
> Created at: 2025-09-10 19:04:29 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276181401  

Many Japanese University students think otherwise. I have active connections to the young community and they actually empathize with me. Thanks for the video anyways, that is a good evidence that western, agitated-by-businessman young people are very different than my colleagues.

---

## Comment 17

> Username: cmazakas  
> Created at: 2025-09-10 19:29:04 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276252736  

> My goal is to revitalize C++. It is impossible without gathering contributions from the young people.  
>   
> Some people write C++ for business, while some people write C++ because it's genuinely fun to hack. That's the difference.  
>   
> It's the same reason that Rust is getting wider audience than C++.  
  
What do you think about Boost getting a Rust chapter? I've been looking for people to unite with.

---

## Comment 18

> Username: saki7  
> Created at: 2025-09-10 19:35:01 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3276267598  

> What do you think about Boost getting a Rust chapter? I've been looking for people to unite with.  
  
I'm not sure what you're talking about. I personally never written Rust in my entire life. The only information I know about Rust is that many young people obsessed with bleeding edge technology tend to choose it as their primary language. Oh wait, I remember that I recently heard that `sudo` on Ubuntu is now rewritten by Rust. Is that what you're talking about? I don't think Boost is capable of hosting a bleeding edge project that rewrites `sudo`.

---

## Comment 19

> Username: siladic  
> Created at: 2025-09-11 05:21:24 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3277786957  

Perhaps we should end this discussion before it turns into a language war. The situation is straightforward: if Spirit continues to hard-require C++23, it will lose at least one user. We cannot—and will not—raise MQTT5’s requirement to C++23, because many people use it and simply cannot upgrade their toolchains.  
  
In particular, MQTT5 is typically used not in hobby projects but in systems running on small, resource-constrained devices with custom SoCs—hardware designed to operate for years. It’s unrealistic to expect the toolchains for these devices to be bleeding-edge at all times. We still want to support developers targeting such platforms. Our goal is to provide a useful library that works with minimal effort. Their needs and projects matter most, not ours.

---

## Comment 20

> Username: klemens-morgenstern  
> Created at: 2025-09-11 11:57:41 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3280220299  

I think the amount of effort this this just caused you is undue, but that doesn't solve the issue.  
  
We need to find a solution quickly (before the next release), unless we can convince @saki7 to reverse the minimum requirement to C++20 (based on the thread, I have doubts that'll happen).  
  
The most obvious is to just vendor the last working version spirit.x3 into boost (into a detail::x3 namespace), which would fix the issue for the next release.  
  
There are two alternatives: use boost.parser, which is similar to spirit but only requires C++17.   
Or you can work with the mini-parser in boost.url as Vinnie proposed. The latter might really improve your compile times.  
  
If you are willing to explore either, I'd highly recommend to join the discussion on the mailing list Vinnie just started, where you will probably get some help.

---

## Comment 21

> Username: biljazovic  
> Created at: 2025-09-11 12:47:47 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3280473482  

> but that doesn't solve the issue  
  
I'm currently trying out Boost.Parser. Given our needs (the parsing we do is not complex), we’ll either go with that or hand-roll something lightweight. In either case, we expect to have it sorted well before October.

---

## Comment 22

> Username: saki7  
> Created at: 2025-09-13 00:50:42 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3287289524  

We (Spirit maintainers) have officially decided to revert all C++23 modernizations.  
Spirit.X3 will work on C++17 mode, effective immediately.  
  
Spirit.X3 is officially feature-frozen and will never introduce breaking changes from now.  
  
We (the Boost Community) have reached a consensus on continuing the development on a brand new repository, Spirit.X4. This won't affect your codebase.  
  
<https://lists.boost.org/archives/list/boost@lists.boost.org/thread/K3EQLEQJHEUBROB6ODUKTZHFP2FDUS2E/?sort=date>

---

## Comment 23

> Username: biljazovic  
> Created at: 2025-09-19 09:33:54 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3311455884  

Thanks @saki7 and everyone involved for the quick action on reverting the C++23 requirement.  
  
We've decided to drop the Spirit dependency regardless. Compilation time is now marginally improved.

---

## Comment 24

> Username: daixtrose  
> Created at: 2025-10-21 16:56:31 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3427758765  

> I will just leave you with one final thought. You are wrong about everything, in particular, what young people want.  
  
@vinniefalco, I am not sure whether I want to follow you here. Did you recognize you were talking to a young person? Does it have to be "**wrong about everything**" when "I completely disagree with you" or "in my perception, young people want something else" would have done?  
  
I give you the credit for [really aiming at improving your way of communication](https://cppalliance.org/vinnie/2023/09/22/Words-Matter-An-Apology.html), but let me give you the feedback that reading this whole thread left me very uneasy, especially given the intercultural nature of the exchange.      
  
The tension between progressing in C++ by following the latest and greatest standard, versus the impossibility (and, in most cases, laziness and unwillingness) to update the toolchain over 10 to 20 years is a vast, unresolved problem in the C++ community, one that is very specific to it.   
  
It is a very delicate problem, and "swinging the hammer" against @saki7, who—at least in my perception—really tried hard to navigate this issue in a balanced and serving manner, was not helpful; to the contrary, having four different incompatible variants of Boost.Spirit, most of which are unmaintained, is not a satisfying solution.   
  
Let us hope that @saki7 is not completely demotivated and continues to create a great new library.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2025-10-21 17:08:49 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/36#issuecomment-3427890356  

You could be right, thank you for the insights.
