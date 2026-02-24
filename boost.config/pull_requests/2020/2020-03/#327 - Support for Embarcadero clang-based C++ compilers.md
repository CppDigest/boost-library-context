# #327 Support for Embarcadero clang-based C++ compilers [Merged]

> Username: eldiener  
> Created at: 2020-03-21 22:55:25 UTC  
> Updated at: 2020-11-25 18:14:44 UTC  
> Merged at: 2020-03-30 14:52:31 UTC  
> Closed at: 2020-03-30 14:52:31 UTC  
> Url: https://github.com/boostorg/config/pull/327  

This is a PR to integrate the Embarcadero C++ clang-based compilers on Windows into Boost config. The Embarcardero C++ clang-based compilers on Windows currently use clang-5.0. The compilers are bcc64 64-bit compiler using clang command line flags, bcc32x 32-bit compiler using clang command line flags, and bcc32c 32-bit compiler accepting the same command line flags as the currently long-standing bcc32. The bcc32x and bcc32x compilers are the exact same compiler codewise.  
  
Already pushed to the Boost Build 'develop' branch is an updated borland.jam toolset which  
also allows the bcc32c compiler to be used, and a new embarcadero.jam troolset which allows  
the bcc32x and bcc64 compilers to be used witrh largely clang command line parameters.  
  
In this PR I have made no attempt to update the bcc32 compiler, but have used my effort to  
integrate bcc32x, bcc32c, and bcc64 into config. Since Embarcadero still uses the same  
\_\_BORLANDC\_\_ and \_\_CODEGEARC\_\_ predefined macros, I have added these macros to Boost config  
for the Embarcadero C++ compilers:  
  
BOOST_BORLANDC = \_\_BORLANDC\_\_ when not using an Embarcadero clang-based compiler  
BOOST_CODEGEARC = \_\_CODEGEARC\_\_ when not using an Embarcadero clang-based compiler  
BOOST_EMBTC = \_\_CODEGEARC\_\_ when using an Embarcadero clang-based compiler  
  
Also added are a number of macros used by Embarcadero themselves in their own  
Boost 1.68 implementation available as an add-on to their C++ Builder RAD product,  
starting with BOOST_EMBTC, but which are not being used further in Boost config.  
You can see these macros in the codegear.hpp file. The name BOOST_EMBTC is Embarcadero's own favorite macro name to refer to their clang-based compilers. I have no idea from where they got that mnemonic.  
  
Since \_\_clang\_\_ is defined for the Embarcadero C++ clang-based compilers, the  
laborious alternative for the macros above would be "!defined(\_\_clang\_\_) &&  
defined(\_\_BORLANDC\_\_)", "!defined(\_\_clang\_\_) && defined(\_\_CODEGEARC\_\_)",  
and "defined(\_\_clang__) && defined(\_\_CODEGEARC\_\_)" respectively. But since I  
would really like, once this PR is accepted, to issue PRs for all other Boost  
libraries where \_\_BORLANDC\_\_ could simply be changed to BOOST_BORLANDC and  
\_\_CODEGEARC\_\_ could simply be changed to BOOST_CODEGEARC, of course given that  
boost/config.hpp has been included which it nearly always is, I think the  
creation of these macros are worth it. I have checked through all the current Boost  
source code and no libraries uses any of these macro names.  
  
The only Boost config testing issues I have found are that the 32-bit compilers,  
which do not define BOOST_NO_EXCEPTIONS, fail the config test. I have reported  
this to Embarcadero as a bug. Maybe I should define BOOST_NO_EXCEPTIONS for these 32-bit  
compilers, but that seems drastic.  
  
Finally I have made no attempt to do anything with bcc32. It is a lost cause.  
But I do think programmers using Embarcadero C++ clang-based compilers, which  
are fairly good at folowing the C++ standard, should be able to use Boost,  
especially since clang-5.0 and the Embarcadero C++ compilers are supported  
through C++17.  
  
This PR also contains a simple fix for the Issue I just posted about the  
config-link-test.   
  
Please take a look. You can obtain a free copy of the latest C++ Builder  
Community Edition at https://www.embarcadero.com/products/cbuilder/starter.  
You can get the latest Boost Build 'develop' to use the borland.jam and  
embarcadero.jam to test the compilers for yourself in Boostg if you like.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-03-25 05:43:26 UTC  
> Url: https://github.com/boostorg/config/pull/327#issuecomment-603652039  

If I resolve the conflicting file and then choose "Commit Merge" does that merge the pull request ? I do not understand why the above thinks that there is a conflict. I have tried to slowly resolve the conflict step by step. I want to resolve the conflict according to this web interface, since just changing autolink.hpp and merging into my fork does not resolve what this interface thinks as a conflict. But I do not want to merge the PR if I can until others look at it and approve it.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-03-25 09:51:42 UTC  
> Url: https://github.com/boostorg/config/pull/327#issuecomment-603745935  

The web interface for conflicts commits to the branch from from which the PR originated: In any case I *think* I have things resolved, but I'll double check the diffs later - it would be great if you could too?

---

## Comment 3

> Username: eldiener  
> Created_at: 2020-03-25 16:38:30 UTC  
> Url: https://github.com/boostorg/config/pull/327#issuecomment-603948998  

Thanks ! I thought it was just trying to merge to the branch of my PR, but I was not sure. I did check autolink and made a final fix.

---

## Comment 4

> Username: eldiener  
> Created_at: 2020-03-26 01:02:07 UTC  
> Url: https://github.com/boostorg/config/pull/327#issuecomment-604168365  

The failed AppVeyor tests alll say:  
  
"Cannot download GitHub repository contents: Forbidden"  
  
I have no idea what this means.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-03-26 15:59:37 UTC  
> Url: https://github.com/boostorg/config/pull/327#issuecomment-604514454  

>The failed AppVeyor tests alll say:  
>  
>"Cannot download GitHub repository contents: Forbidden"  
>  
>I have no idea what this means.  
  
Networking SNAFU I believe, it's been happening a lot and it's mighty annoying.  Hitting "re-run incomplete" usually clears things, though last time I had to manually cycle through that several times before they all completed :(

---

## Comment 6

> Username: eldiener  
> Created_at: 2020-03-26 17:05:56 UTC  
> Url: https://github.com/boostorg/config/pull/327#issuecomment-604552201  

I see you have done that, but where do you choose the "re-run incomplete" which you mentioned.

---

## Comment 7

> Username: eldiener  
> Created_at: 2020-03-29 02:34:53 UTC  
> Url: https://github.com/boostorg/config/pull/327#issuecomment-605549291  

Any objections to merging this PR ?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2020-03-30 15:04:00 UTC  
> Url: https://github.com/boostorg/config/pull/327#issuecomment-606055038  

Thanks Edward, that all looks fine!

---

## Review 9 [Commented]

> Username: Kojoley  
> Created_at: 2020-05-03 19:35:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/327#pullrequestreview-404650299  

📁 include/boost/config/compiler/codegear.hpp

```diff
 141 |+ 
 142 |+ # define BOOST_CODEGEARC  __CODEGEARC__
 143 |+ # define BOOST_BORLANDC   __BORLANDC__
```

> Username: Kojoley  
> Created_at: 2020-05-03 19:35:01 UTC  
> Updated_at: 2020-05-03 19:35:02 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419148977  

This breaks the whole point of `BOOST_compiler` macros that should tell if it is really the particular compiler and not an other that mimics it.

> Username: eldiener  
> Created_at: 2020-05-03 19:45:49 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419150347  

It does not. The definition is only made when __CODEGEARC__ is originally defined, meaning the Borland/Codegear/Embarcadero C++ compilers, and when __clang__ is not defined, meaning that it is not the Embarcadero C++ clang-based compiler(s). There is no other compiler that defines __BORLANDC__ and __CODEGEARC__ anywhere.

> Username: Kojoley  
> Created_at: 2020-05-03 19:47:44 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419150599  

It does, you are defining both simultaneously.

> Username: eldiener  
> Created_at: 2020-05-03 19:52:04 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419151165  

What is the difference. Both \_\_BORLANDC\_\_ and \_\_CODEGEARC\_\_ are always defined to the same value whenever \_\_CODEGEARC\_\_ is defined. I want both BOOST_BORLANDC and BOOST_CODEGEARC defined whenever the non-clang-based compilers are detected.

> Username: Kojoley  
> Created_at: 2020-05-03 19:57:58 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419151911  

There is no point in having both when they mean the same thing, it only produces a confusion

> Username: eldiener  
> Created_at: 2020-05-03 20:02:41 UTC  
> Updated_at: 2020-05-03 20:02:42 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419152518  

Both are needed because there is code in other libraries referrring to the old bcc32 compiler using both \_\_BORLANDC\_\_ and \_\_CODEGEARC\_\_.

> Username: glenfe  
> Created_at: 2020-05-04 04:24:36 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419208588  

@jzmaddock is the intention that there should only ever be one BOOST_COMPILER macro defined?

> Username: jzmaddock  
> Created_at: 2020-05-04 10:47:45 UTC  
> Updated_at: 2020-05-04 10:47:46 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419351737  

>is the intention that there should only ever be one BOOST_COMPILER macro defined?  
  
Yes but that's not really the mistake here: Borland and Codegear are the same compiler give or take a few updates, we should probably have kept the same header and name.  
  
Personally I think it's fine to define both under these circumstances.  Note that with the Sun->Oracle name change we've basically just ignored the issue and continued calling it "sunpro" and no one seems to have complained (yet).


---

## Review 10 [Commented]

> Username: Kojoley  
> Created_at: 2020-05-03 19:40:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/327#pullrequestreview-404650802  

📁 include/boost/config/compiler/borland.hpp

```diff
  19 | // last known compiler version:
  20 |- #if (__BORLANDC__ > 0x613)
  20 |+ #if (__BORLANDC__ > 0x740)
```

> Username: Kojoley  
> Created_at: 2020-05-03 19:40:55 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419149700  

`0x740` is not a Borland compiler even

> Username: eldiener  
> Created_at: 2020-05-03 19:48:04 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419150618  

Please look at the current Embacadero C++ compilers and you will realize that the latest release defines __BORLANDC__ and __CODEGEARC__ to 0x740. Really, sir, you need to do some invesigation before you make these assertions.

> Username: Kojoley  
> Created_at: 2020-05-03 19:54:32 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419151497  

This header is for classic Borland compiler and not for Embarcadero Clang-based not a Borland compiler, is not it?

> Username: eldiener  
> Created_at: 2020-05-03 20:09:03 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419153286  

The header is used when \_\_CODEGEARC\_\_ is not defined, so it is for some older version of bcb. You are right that I could have left the last known compiler version at 0x613, but even that does not appear to be the actual last known version before \_\_CODEGEARC\_\_ was defined. So to play it safe I just allow the last known version to be whatever the current last version for Embarcadero C++ compilers now is.

> Username: Kojoley  
> Created_at: 2020-05-03 20:11:34 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419153568  

To play safe it should be reverted because now it means that __CODEGEARC__ compilers after 0x613 were tested and the `BOOST_NO_` macros filled

> Username: eldiener  
> Created_at: 2020-05-04 02:44:12 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r419195077  

I reverted it back to 0x613 in this PR.


---

## Review 11 [Commented]

> Username: tanzislam  
> Created_at: 2020-11-25 15:51:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/327#pullrequestreview-538634228  

📁 include/boost/config/auto_link.hpp

```diff
 195 |+ #    elif defined(BOOST_EMBTC_WIN64)
 196 |+ #      define BOOST_LIB_TOOLSET "bcb64"
 197 |+ #    endif
```

> Username: tanzislam  
> Created_at: 2020-11-25 15:51:02 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r530474251  

I'm changing this logic to simply `bcb` in #346. This makes it easier to match the toolset name used by Boost.Build (in the "versioned" layout).

> Username: eldiener  
> Created_at: 2020-11-25 16:19:02 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r530494386  

The toolset used by Boost.Build to build Boost for the Embarcadero C++ clang-based compilers should be 'embarcadero.jam' and not 'borland.jam'.  
  
I think it is very bad if building for the old bcc32 compiler ( although it will fail miserably with current Boost ) should create the exact same library names as building for the clang-based compilers. That is why I chose different names for the Embarcadero C++ clang-based compilers. However I think you are right that there should not be different toolsets based on 32bit or 64bit builds, as no other compiler does that, so I would choose 'embtc' or maybe just 'emb', for the Embarcadero C++ clang-based compilers rather than the bcb32/bcb64 above. But I would not choose the same toolset name as for the old bcc32 compiler, as it will be confusing to the Embarcadero end-user, who may be used to seeing that name refer to the old compiler.

> Username: tanzislam  
> Created_at: 2020-11-25 16:25:42 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r530498956  

There is nowadays (since v10.3.3) a linker warning against this problem: https://blogs.embarcadero.com/issues-mixing-classic-and-clang-objects-linker-warnings-improving-app-stability/

> Username: eldiener  
> Created_at: 2020-11-25 18:14:25 UTC  
> Updated_at: 2020-11-25 18:14:44 UTC  
> Url: https://github.com/boostorg/config/pull/327#discussion_r530564005  

In Embarcadero's home-grown version of Boost 1.68/1.70 their lines are:  
  
```  
// Embarcadero Clang based compilers:  
#    if defined(BOOST_EMBTC_WIN32C)  
#      define BOOST_LIB_TOOLSET "bcb32c"  
#    elif defined(BOOST_EMBTC_WIN64)  
#      define BOOST_LIB_TOOLSET "bcb64"  
#    endif  
```  
  
I changed this slightly to "bcb32" from "bcb32c", since the Embarcadero C++ clang-based compilers are bcb32c and bcb32x, with the exact same clang-based code in all situations, and the only difference being that bcb32c uses the old bcc32 compiler options while bcb32x uses clang-based compiler options. They created separate names for their Embarcadero C++ clang-based BOOST_LIB_TOOLSET and their old bcc32 BOOST_LIB_TOOLSET. I think that separation should continue. But I agree with you that Boost usually does not create separate BOOST_LIB_TOOLSET names for 32bit and 64bit builds. So I still think that giving a BOOST_LIB_TOOLSET name that is different from the 'bcb' name is the way to go. So I would choose 'embtc', which is the general mnemonic Embarcadero used in their home-grown version of 1.68/1.70 to indicate the clang-based compilers.


---
