# #231 - Please add Outcome to super repo for 1.70 release [Closed]

> Username: ned14  
> Created at: 2018-12-13 21:12:05 UTC  
> Updated at: 2019-01-22 23:40:05 UTC  
> Closed at: 2019-01-12 23:26:35 UTC  
> Url: https://github.com/boostorg/boost/issues/231  

https://github.com/ned14/boost-outcome

---

## Comment 1

> Username: pdimov  
> Created at: 2018-12-20 01:56:04 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-448830075  

You need to transfer the repo to an owner of boostorg, who would then transfer it to boostorg.

---

## Comment 2

> Username: ned14  
> Created at: 2018-12-27 05:56:25 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-450081187  

Firstly, my apologies for taking so long to get back to you. I was in Yosemite Valley on vacation where my European mobile phone could not get a signal from weird US frequencies, and thus the two factor authentication for logging into Github could not work. I am now on the coast in San Simeon on my second leg of vacation, where I can receive SMS 2FA.  
  
Secondly, a very Merry Christmas to you Peter, and thanks for I assume taking up the role of sending boost-outcome into boostorg.  
  
boost-outcome is now in your ownership. My thanks to you in advance.

---

## Comment 3

> Username: pdimov  
> Created at: 2018-12-27 06:36:20 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-450086507  

Here it is: https://github.com/boostorg/outcome  
  
You'll need a `develop` branch though. Without it, I can't add it as a submodule on the `develop` branch of the superproject. I could create one off your current `master`, but since your repo is updated by a script, this may not be enough.

---

## Comment 4

> Username: djarek  
> Created at: 2018-12-27 16:00:49 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-450178682  

Would it be possible for the auto-generated commits in https://github.com/boostorg/outcome to at least have a reference to the original commit in the source repository? Makes it easier to follow changes in git.

---

## Comment 5

> Username: ned14  
> Created at: 2018-12-27 19:08:39 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-450212007  

Cool, thanks for the transfer. I can't fix the develop branch issue until I return to Europe, I have only my phone with me, no computers. I agree the commit history needs to be improved, and I will do that. We also need to decide how to handle the documentation, whether generation from Hugo or Travis doing it and pushing prebuilt static HTML is best.

---

## Comment 6

> Username: ned14  
> Created at: 2019-01-10 15:47:38 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453143447  

Ok, we now have a commit by commit exact copy of standalone, with master and develop branches replicated including commit history and a web link to the original on github.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-01-11 21:53:09 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453668910  

Submodule added to develop, first results are in: https://www.boost.org/development/tests/develop/developer/outcome.html  
  
We'll need to figure out something for the docs though.

---

## Comment 8

> Username: ned14  
> Created at: 2019-01-11 22:20:53 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453675634  

Docs can be seen at https://boostorg.github.io/outcome/  
  
Would a simple git submodule of that into boostorg/website suffice?  
  
It's weird that the constexpr tests are failing above. They pass just fine on https://travis-ci.org/boostorg/outcome/jobs/478534166. Maybe I'll add more, newer compilers, see if there is something surprising there.

---

## Comment 9

> Username: ned14  
> Created at: 2019-01-11 22:25:19 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453676610  

Ah, the literal test fail is due to your new literal `boost::error_code`. Nice one!

---

## Comment 10

> Username: pdimov  
> Created at: 2019-01-11 22:27:08 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453677017  

> Would a simple git submodule of that into boostorg/website suffice?  
  
I doubt it. The doc build executes `libs/outcome/doc/Jamfile`, then takes whatever this has produced in `libs/outcome/doc/html` and uploads it... somehow... somewhere, then https://www.boost.org/doc/libs/develop/libs/outcome/doc/html/index.html shows that uploaded content. It's all magic and duct tape that probably nobody except @danieljames understands. It's technically possible to submodule gh-pages into doc/html, I suppose... not sure. Or, you could `git subtree` the docs into doc/html it and update them from the script?

---

## Comment 11

> Username: pdimov  
> Created at: 2019-01-11 22:27:42 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453677146  

The constexpr tests failed for me locally last time I tried them. I'll update to latest develop and check again.

---

## Comment 12

> Username: pdimov  
> Created at: 2019-01-11 22:31:30 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453678059  

```  
compile-c-c++ ..\..\bin.v2\libs\outcome\test\constexpr.test\msvc-14.1\debug\thre  
ading-multi\tests\constexpr.obj  
constexpr.cpp  
test\tests\constexpr.cpp(45): error C2338: result<int> is a literal type!  
```  
and others on msvc-14.1.

---

## Comment 13

> Username: ned14  
> Created at: 2019-01-11 22:31:54 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453678132  

Don't worry about the constexpr test, it fails because of your shiny new error code. I've removed the test here.  
  
Can we disable msvc-1.40 on the regression matrix? It'll never, ever pass.  
  
I was explicitly told not to place git submodules into boostorg repos. However, Jamfile could locally git submodule add?

---

## Comment 14

> Username: pdimov  
> Created at: 2019-01-11 22:34:23 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453678670  

There's nothing in "the rules" that prevents you submoduling your own submodule, but the problem is that nobody's going to `git submodule update` it. :-)

---

## Comment 15

> Username: pdimov  
> Created at: 2019-01-11 22:37:16 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453679311  

And, you'll still need to update the submodule (commit) somehow. Which is more or less the same amount of work as updating `doc/html` itself. As you already have a script... maybe you can just keep the generated docs in the repo and update them from the script.

---

## Comment 16

> Username: pdimov  
> Created at: 2019-01-11 22:40:13 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453679915  

To disable msvc-14.0, just add a requirement for cxx14_constexpr, in addition to your current one for cxx14_variable_templates.

---

## Comment 17

> Username: ned14  
> Created at: 2019-01-11 22:44:24 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453680830  

I thought there was a policy against pushing a bunch of static HTML when it's generated? Thanks for the tip regarding disabling msvc-14.0.

---

## Comment 18

> Username: pdimov  
> Created at: 2019-01-11 22:52:35 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453682585  

Having doc/html in the primary repo isn't good because of pull requests against the doc source often not being reflected in the generated html, but since your primary repo isn't here so you'll be applying the pull requests there, this doesn't really apply. It will generate a bit of churn in the revision history, but there are many other Boost libraries that have static html too. So it's not unheard of, and in this case, seems like the most straightforward approach.

---

## Comment 19

> Username: pdimov  
> Created at: 2019-01-11 22:56:52 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453683456  

This is the other error on msvc-14.1:  
```  
compile-c-c++ ..\..\bin.v2\libs\outcome\test\hooks.test\msvc-14.1\debug\threadin  
g-multi\tests\hooks.obj  
hooks.cpp  
test\tests\hooks.cpp(72): error C2280: 'boost::outcome_v2::basic_result<R,hook_t  
est::error_code,boost::outcome_v2::policy::fail_to_compile_observers>::basic_res  
ult<int,void>(T &&,boost::outcome_v2::basic_result<R,hook_test::error_code,boost  
::outcome_v2::policy::fail_to_compile_observers>::implicit_constructors_disabled  
_tag)': attempting to reference a deleted function  
        with  
        [  
            R=int,  
            T=int  
        ]  
```  
...  
```  
c:\boost-git\develop\libs\outcome\include\boost\outcome\config.hpp(251): error C  
2065: 'value': undeclared identifier  
c:\boost-git\develop\libs\outcome\include\boost\outcome\config.hpp(251): error C  
2131: expression did not evaluate to a constant  
c:\boost-git\develop\libs\outcome\include\boost\outcome\config.hpp(251): note: a  
 non-constant (sub-)expression was encountered  
```

---

## Comment 20

> Username: ned14  
> Created at: 2019-01-12 19:12:39 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453773532  

Yeah I saw that failure as well. Outcome was used by the Visual C++ team to debug MSVC, it's part of their test suite. So various older point releases fail the test suite. Latest VS2017 works fine, you can see there is another msvc-14.1 runner where it all passes. Will failing tests be a problem for release?

---

## Comment 21

> Username: ned14  
> Created at: 2019-01-12 19:16:36 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453773763  

I also see that GCC 8 is ICEing in the constexpr tests. That's a long standing open bug in GCC, open since GCC 7, I'm subscribed to it, but they can't seem to figure out a solution.

---

## Comment 22

> Username: pdimov  
> Created at: 2019-01-12 20:14:28 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453777674  

> Latest VS2017 works fine  
  
Not for me, I was on latest when I tried it, it updated today to even more latest, still fails with the same error.

---

## Comment 23

> Username: pdimov  
> Created at: 2019-01-12 20:15:38 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453777744  

No, a failing test is not a problem for the release.

---

## Comment 24

> Username: pdimov  
> Created at: 2019-01-12 20:17:13 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453777819  

Ah, it passes with cxxstd=17, fails with cxxstd=14.

---

## Comment 25

> Username: ned14  
> Created at: 2019-01-12 23:26:35 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453788993  

> Ah, it passes with cxxstd=17, fails with cxxstd=14.  
  
Oh cool. If I make standalone Outcome fail the same way, it'll automatically show up in MSVC's test suite, and get itself auto-fixed. Thanks for the tip!  
  
I figure this is working now, the docs now appear as static html in doc/html, so I guess this ticket is complete. Thanks for all your help Peter. Now just got to get the documentation finished.

---

## Comment 26

> Username: pdimov  
> Created at: 2019-01-12 23:39:13 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-453789560  

Yes, docs seem to work fine: https://www.boost.org/doc/libs/develop/libs/outcome/doc/html/index.html

---

## Comment 27

> Username: danieljames  
> Created at: 2019-01-22 23:40:05 UTC  
> Url: https://github.com/boostorg/boost/issues/231#issuecomment-456608392  

FWIW The documentation on the website is just taken from the snapshot on bintray. The documentation is generated by Rene's CI build scripts. You can check in generated html file if you want, it's a pain to manage, but whatever works.
