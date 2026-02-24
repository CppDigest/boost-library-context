# #21 Suppress weak vtables warning [Merged]

> Username: Kojoley  
> Created at: 2016-08-31 16:35:42 UTC  
> Updated at: 2020-04-05 21:47:39 UTC  
> Merged at: 2020-04-05 21:47:39 UTC  
> Closed at: 2020-04-05 21:47:39 UTC  
> Url: https://github.com/boostorg/optional/pull/21  



---

## Comment 1

> Username: akrzemi1  
> Created_at: 2016-08-31 20:12:33 UTC  
> Url: https://github.com/boostorg/optional/pull/21#issuecomment-243885876  

I am not sure I want to conceal this warning. Clang's diagnosis is correct: boost::optional does have this vtable problem. But I need it this way in order for the library to remain header-only. A typical solution in this case is for the users to annotate boost libraries as system libraries. Then compilers do not emit warnings from boost.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2016-08-31 20:18:50 UTC  
> Url: https://github.com/boostorg/optional/pull/21#issuecomment-243887704  

Consider following:  
  
```  
In file included from qi/actions.cpp:15:  
In file included from ../../../boost/spirit/include/qi_operator.hpp:16:  
In file included from ../../../boost/spirit/home/qi/operator.hpp:14:  
In file included from ../../../boost/spirit/home/qi/operator/sequence.hpp:15:  
In file included from ../../../boost/spirit/home/qi/operator/sequence_base.hpp:15:  
In file included from ../../../boost/spirit/home/qi/domain.hpp:18:  
In file included from ../../../boost/spirit/home/support/context.hpp:18:  
In file included from ../../../boost/spirit/home/support/nonterminal/expand_arg.hpp:20:  
In file included from ../../../boost/spirit/home/support/string_traits.hpp:16:  
In file included from ../../../boost/spirit/home/support/container.hpp:21:  
In file included from ../../../boost/optional.hpp:15:  
In file included from ../../../boost/optional/optional.hpp:28:  
../../../boost/optional/bad_optional_access.hpp:22:7: warning: 'bad_optional_access' has no out-of-line virtual method definitions; its vtable will be emitted in every translation unit [-Wweak-vtables]  
class bad_optional_access : public std::logic_error  
      ^  
```  
  
With the patch warning is gone. So why the PR is unacceptable for you?

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2016-08-31 20:44:52 UTC  
> Url: https://github.com/boostorg/optional/pull/21#issuecomment-243895357  

You chose to have Clang report warnings upon types causing potential multiple definitions of virtual tables (this is what -Wweak-vtables is about). Clang reports one for `boost::bad_optional_access` because the class does have this property. If your intention is to see warnings about potential multiple vtables, why would I want to hide one in `boost::bad_optional_access`?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2016-08-31 21:05:23 UTC  
> Url: https://github.com/boostorg/optional/pull/21#issuecomment-243901488  

Like in other cases with different warnings I want to have maximum warning level to not miss something bad crawl into the code. As you mentioned I have possibility to mark include as a system with `-isystem` but it is not currently possible with Boost.Build and this will not change the status on the https://svn.boost.org/trac/boost/wiki/WarningFixes. Being the author of the library you know what warning are errors, and I can understand if you reject the patch if it only cover actual problem it, but in the current situation you understand that nothing cannot be done to the code so compiler can understand where to put virtual table. That is why I think the PR is the best what can be done to the issue. If you wish me to put a note to the docs about the warning suppression - feel free to ask.

---

## Comment 5

> Username: akrzemi1  
> Created_at: 2016-09-01 06:41:14 UTC  
> Url: https://github.com/boostorg/optional/pull/21#issuecomment-243989912  

I still feel it is the wrong way to go. I may ultimately give in to your suggestion, but so far I am not convinced enough. The page you linked says that we should be removing warnings _within reason_, when it adds value.  
  
With this particular warning, -Wweak-vtables, I am not sure you should just put it on, only because you can. Forget boost::optional; think of your own classes; what does the warning say to you? What potential problem do you expect, if this warning is triggered?

---

## Comment 6

> Username: Flamefire  
> Created_at: 2020-03-23 08:17:16 UTC  
> Url: https://github.com/boostorg/optional/pull/21#issuecomment-602448053  

On the warning: It doesn't seem to be a real problem, see e.g. https://stackoverflow.com/questions/23746941/what-is-the-meaning-of-clangs-wweak-vtables  
  
As you argued: This is intended: You are aware that multiple copies of the vtable are to be emitted and that this is fine. (This is what you said, right?) So given that it is perfectly fine to silence the warning (maybe with a comment) as this is what warning silencing is for: Hide warnings that are no errors. There is no use to see this warning in a build of Boost or (even worse) a dependent project. I fully agree with @Kojoley that downstream users don't want to see expected warnings, especially as they can't do anything about them. E.g. I always build my libraries with -Werror on CI and as many warnings enabled as possible. If an upstream lib throws a needless warning this breaks this and ultimately the further downstream users nice warning-free build.

---
