# #441 - Conflicts with Bool, None, defined in Xlib.h [Closed]

> Username: ghost  
> Created at: 2019-02-19 19:49:54 UTC  
> Updated at: 2019-04-01 17:38:59 UTC  
> Closed at: 2019-04-01 17:38:58 UTC  
> Url: https://github.com/boostorg/hana/issues/441  

I stumbled upon a conflict with constants defined in Xlib. The following test case fails to compile:  
  
```  
$ cat t.cc; g++ -c t.cc 2>&1  
#include <X11/Xlib.h>  
#include <boost/hana.hpp>  
In file included from t.cc:1:  
/usr/include/boost/hana/eval_if.hpp: In member function ‘constexpr decltype(auto) boost::hana::eval_if_t::operator()(Cond&&, Then&&, Else&&) const’:  
/usr/include/boost/hana/eval_if.hpp:30:15: error: expected nested-name-specifier before ‘int’  
         using Bool = typename hana::tag_of<Cond>::type;  
               ^~~~  
In file included from /usr/include/boost/hana/core/dispatch.hpp:14,  
                 from /usr/include/boost/hana/accessors.hpp:17,  
                 from /usr/include/boost/hana.hpp:58,  
                 from t.cc:2:  
/usr/include/boost/hana/eval_if.hpp:31:24: error: incomplete type ‘boost::hana::Logical<int>’ used in nested name specifier  
         using EvalIf = BOOST_HANA_DISPATCH_IF(eval_if_impl<Bool>,  
                        ^~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/hana/eval_if.hpp:31:24: error: template argument 1 is invalid  
         using EvalIf = BOOST_HANA_DISPATCH_IF(eval_if_impl<Bool>,  
                        ^~~~~~~~~~~~~~~~~~~~~~  
[...]  
In file included from /usr/include/X11/Xlib.h:44,  
                 from t.cc:1:  
/usr/include/boost/hana/none.hpp: In member function ‘constexpr auto boost::hana::none_t::operator()(Xs&&) const’:  
/usr/include/boost/hana/none.hpp:27:15: error: expected nested-name-specifier before numeric constant  
         using None = BOOST_HANA_DISPATCH_IF(none_impl<S>,  
               ^~~~  
In file included from /usr/include/boost/hana.hpp:147,  
                 from t.cc:2:  
/usr/include/boost/hana/none.hpp:36:20: error: expected ‘;’ before ‘::’ token  
         return None::apply(static_cast<Xs&&>(xs));  
                    ^~  
/usr/include/boost/hana/none.hpp:36:22: error: ‘::apply’ has not been declared  
         return None::apply(static_cast<Xs&&>(xs));  
                      ^~~~~  
/usr/include/boost/hana/none.hpp:36:22: note: suggested alternative:  
In file included from /usr/include/boost/hana/functional/curry.hpp:15,  
                 from /usr/include/boost/hana/ap.hpp:21,  
                 from /usr/include/boost/hana.hpp:68,  
                 from t.cc:2:  
/usr/include/boost/hana/functional/apply.hpp:81:23: note:   ‘boost::hana::apply’  
     constexpr apply_t apply{};  
                       ^~~~~  
  
Compilation exited abnormally with code 1 at Tue Feb 19 14:45:02  
```  
  
Bool is defined to `int` and None to something else. I can't think of a good enough workaround (other than including hana at the very top of my project, before any X11 headers).

---

## Comment 1

> Username: pfultz2  
> Created at: 2019-02-20 03:58:36 UTC  
> Url: https://github.com/boostorg/hana/issues/441#issuecomment-465412508  

Its best to isolate X11 in a separate TU since it defines macros that can break a lot of code. Or you could use a header wrapper like what chromium does [here](https://chromium.googlesource.com/chromium/src.git/+/01b976ce4bf294bccc6c9ed23bf41d525b9c3aaf/ui/gfx/x/x11.h).

---

## Comment 2

> Username: ldionne  
> Created at: 2019-04-01 17:38:58 UTC  
> Url: https://github.com/boostorg/hana/issues/441#issuecomment-478674371  

@nikkoara  Thanks for reporting.  
  
However, I think the problem here is Xlib being misbehaved. It's very hard to guard a library against all potential problems created by macros in other libraries. I would instead file a bug against Xlib.
