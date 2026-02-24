# #14 Fix gcc 9 deprecated copy warnings by explicitly defaulting default a… [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2019-03-23 00:22:29 UTC  
> Updated at: 2020-05-02 16:01:54 UTC  
> Closed at: 2020-05-02 16:01:54 UTC  
> Url: https://github.com/boostorg/proto/pull/14  

…nd copy constructor if the compiler supports it.

---

## Review 1 [Commented]

> Username: ericniebler  
> Created_at: 2019-08-05 22:06:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/proto/pull/14#pullrequestreview-271029322  

📁 include/boost/proto/detail/expr.hpp

```diff
 149 |+             /// warnings.
 150 |+             BOOST_FORCEINLINE expr() = default;
 151 |+             BOOST_FORCEINLINE expr(const expr&) = default;
```

> Username: ericniebler  
> Created_at: 2019-08-05 22:06:10 UTC  
> Updated_at: 2019-08-05 22:37:59 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r310811333  

This suppresses the generation of the move constructor.

> Username: Romain-Geissler-1A  
> Created_at: 2019-08-05 22:39:08 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r310819493  

> Requesting changes...  
  
Hi,  
  
I have implemented these changes.  
  
Cheers,  
Romain

> Username: ericniebler  
> Created_at: 2019-08-05 22:44:58 UTC  
> Updated_at: 2019-08-05 22:45:04 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r310820828  

I'm also concerned that it has the effect of suppressing the generation of the built-in `operator=`. That is potentially behavior-changing.  
  
On deeper reflection, I'm not comfortable with this change at all. Sorry.

> Username: Romain-Geissler-1A  
> Created_at: 2019-08-05 22:51:19 UTC  
> Updated_at: 2019-08-05 22:51:20 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r310822329  

Well, the gcc warning is all about foreseeing an upcoming change mandated by C++ standard, where defining the copy operator explictely will eventually prevent the implicit copy constructor to be issued. So kind of the same things you are worried about here. So if not fixed now, the problem will eventually come.  
  
If these changes are not ok, would it be acceptable to silence the gcc warnings for now ?

> Username: ericniebler  
> Created_at: 2019-08-05 23:06:27 UTC  
> Updated_at: 2019-08-05 23:06:28 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r310825542  

The other issue with this change is that it changes `expr` from being an aggregate to a non-aggregate. I suspect that interferes with aggregate initialization. But that would break everything. I'm confused how this works.

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-09 22:48:18 UTC  
> Updated_at: 2020-03-09 22:49:05 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r390003263  

Hi @ericniebler,  
  
When trying to migrate to C++20 and gcc 10, I am hitting again this issue, this time in expr_variadic.hpp. Since indeed my change would delete the aggregate constructor (some subtleties I actually didn't know), I guess the only short term option would be to simply silence the gcc warnings. I am not sure if that works (I don't know if gcc pragmas are taken at the location the class is defined or at the location the class is used). If it works, would you be ok for sur a pull request ?  
  
Anyway, C++ is deprecating the usage of implicit copy constructor when an explicit copy assignment operator exists, and such case are happening already. One day there is no doubt the standard will remove this behavior entirely, and that day a better solution than just silencing warnings will have to be found. Since you are somehow involved in the standardization process, I guess you are more knowledgeable than me to actually fix it when it comes ;)  
  
Cheers,  
Romain

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-09 23:48:58 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r390022084  

Apparently enclosing the whole file expr_variadic.hpp with pragma diagnostics ignoring `-Wdeprecated-copy` doesn't help, it has to be used at call site. In my case:  
  
```  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/xpressive/xpressive_static.hpp:24,  
                 from src/functional_tests/ApplicativeEvent.cpp:7:   
/data/mwrep/res/osp/Boost/20-0-0-0/include/boost/xpressive/regex_primitives.hpp: In static member function ‘static boost::xpressive::detail::basic_mark_tag boost::xpressive::mark_tag::make_tag(int)’:   
/data/mwrep/res/osp/Boost/20-0-0-0/include/boost/xpressive/regex_primitives.hpp:548:16: error: implicitly-declared ‘constexpr boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::xpressive::detail::mark_placeholder>, 0>::expr(const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::xpressive::detail::mark_placeholder>, 0>&)’ is deprecated [-Werror=deprecated-copy]  
  548 |         return mark;  
      |                ^~~~  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/proto/detail/expr.hpp:4,  
                 from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/proto/expr.hpp:137,  
                 from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/proto/core.hpp:17,  
                 from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/xpressive/regex_primitives.hpp:28,  
                 from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/xpressive/xpressive_static.hpp:24,  
                 from src/functional_tests/ApplicativeEvent.cpp:7:   
/data/mwrep/res/osp/Boost/20-0-0-0/include/boost/proto/detail/preprocessed/expr_variadic.hpp:99:9: note: because ‘boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::xpressive::detail::mark_placeholder>, 0>’ has user-provided ‘const boost::proto::exprns_::expr<boost::proto::tagns_::tag::assign, boost::proto::argsns_::list2<boost::proto::exprns_::expr<Tag, boost::proto::argsns_::term<Arg>, 0>&, const boost::proto::exprns_::expr<Tag, boost::proto::argsns_::term<Arg>, 0>&>, 2> boost::proto::exprns_::expr<Tag, boost::proto::argsns_::term<Arg>, 0>::operator=(const boost::proto::exprns_::expr<Tag, boost::proto::argsns_::term<Arg>, 0>&) [with Tag = boost::proto::tagns_::tag::terminal; Arg0 = boost::xpressive::detail::mark_placeholder]’  
   99 |         operator =(expr const &a)  
      |         ^~~~~~~~  
```  
  
I am not sure if we want to start chasing down all users of proto and add these pragmas there.  
  
What if in order to fix the aggregate issue we did explicitly define constructors taking the different member initializers as argument ? And what if the copy constructor would be protected by something like  
  
```  
proto::expr<proto::tag::assign, list2<expr &, expr const &>, 2> const  
```  
  
which you use already for the copy assignment operator ?  
  
Cheers,  
Romain

> Username: Romain-Geissler-1A  
> Created_at: 2020-03-18 17:04:21 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r394504234  

@ericniebler Do you think it would make sense to ask standard people to find out some C++23 syntax so that we can express that we want to have the aggregate initialization constructor available and defaulted in a class, despite having defined other constructors (like the copy or the default one) ?

> Username: Kojoley  
> Created_at: 2020-04-05 19:03:06 UTC  
> Updated_at: 2020-04-05 19:03:07 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r403743392  

> The other issue with this change is that it changes expr from being an aggregate to a non-aggregate. I suspect that interferes with aggregate initialization. But that would break everything. I'm confused how this works.  
  
Every major compiler considers it an aggregate (https://godbolt.org/z/3K8jtv), even in C++17 mode, where it was made non-aggregate (https://eel.is/c++draft/diff.cpp17.dcl.dcl#3).

> Username: Kojoley  
> Created_at: 2020-04-05 20:05:06 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r403750231  

Ah, the change is for C++20, and I was thinking it is retroactive while it seems to be not.

> Username: Kojoley  
> Created_at: 2020-04-05 20:15:35 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r403751444  

I filed a bug about this case https://gcc.gnu.org/bugzilla/show_bug.cgi?id=94492

> Username: Romain-Geissler-1A  
> Created_at: 2020-04-05 20:24:42 UTC  
> Updated_at: 2020-04-05 20:25:09 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r403752500  

On our side, while this patch worked for us while we were using C++17 (maybe it was wrong, but in the use cases we had it did not show issue), however when we moved to C++20 indeed the fact that the "aggregate constructor" wasn't allowed anymore, it indeed broke the rest of the boost libraries using it.  
  
We internally decided to stop patching Proto this way, and instead we applied a big "sed" to all boost include files from libraries proto, phoenix, spirit and xpressive to inject in every of this header a pragma to silence the warning (because we still want to have this warning for other libraries, and for our own code). We were hoping that one day someone would come with a solution, either by changing boost, the compilers, or even the C++ standard (because I think the compiler is only implementing what the standard says, it is indeed deprecated).

> Username: Kojoley  
> Created_at: 2020-04-05 20:47:28 UTC  
> Updated_at: 2020-04-05 20:47:29 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r403754911  

> On our side, while this patch worked for us while we were using C++17 (maybe it was wrong, but in the use cases we had it did not show issue), however when we moved to C++20 indeed the fact that the "aggregate constructor" wasn't allowed anymore, it indeed broke the rest of the boost libraries using it.  
  
I though that [P1008](http://wg21.link/p1008) will be applied retroactively, but in fact there is no even a warning in C++11/14/17 mode.  
  
> We internally decided to stop patching Proto this way, and instead we applied a big "sed" to all boost include files from libraries proto, phoenix, spirit and xpressive to inject in every of this header a pragma to silence the warning (because we still want to have this warning for other libraries, and for our own code).  
  
IIUC it should not have helped as the warning is issued at the use site and you have to disable it there, it *your* code. And this is the most cruel part about this GCC diagnostic.  
  
> because I think the compiler is only implementing what the standard says, it is indeed deprecated  
  
Yes, the warning itself is about what the standard says, but currently it is unusable because:  
1. the deprecation was issued in C++11 where it was possible to fix it without making a type non-aggregate,  
2. you cannot turn the warning on only for you code,  
3. there are false-positives https://gcc.gnu.org/bugzilla/show_bug.cgi?id=92145

> Username: Romain-Geissler-1A  
> Created_at: 2020-04-05 20:57:02 UTC  
> Url: https://github.com/boostorg/proto/pull/14#discussion_r403755977  

> IIUC it should not have helped as the warning is issued at the use site and you have to disable it there, it your code. And this is the most cruel part about this GCC diagnostic.  
  
Actually it did, because in all the use case I was shown, the call sites were inside the cited boost libraries. We never use ourselves boost proto, we use it only indirectly through other boost libraries.


---

## Review 2 [Commented]

> Username: ericniebler  
> Created_at: 2019-08-05 22:06:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/proto/pull/14#pullrequestreview-271029549  

You need to also default the move constructor.

---

## Review 3 [Changes requested]

> Username: ericniebler  
> Created_at: 2019-08-05 22:08:02 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/proto/pull/14#pullrequestreview-271029922  

Requesting changes...

---

## Comment 4

> Username: ericniebler  
> Created_at: 2020-04-30 23:10:28 UTC  
> Url: https://github.com/boostorg/proto/pull/14#issuecomment-622166700  

Did #24 fix this?

---

## Comment 5

> Username: Kojoley  
> Created_at: 2020-04-30 23:18:20 UTC  
> Url: https://github.com/boostorg/proto/pull/14#issuecomment-622169538  

Only for Clang, GCC depends on https://gcc.gnu.org/bugzilla/show_bug.cgi?id=94492.

---
