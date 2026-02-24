# #45 - C++20 compilation failure disconnecting slots with boost::bind [Closed]

> Username: richmills3  
> Created at: 2023-02-09 10:52:27 UTC  
> Updated at: 2023-02-12 18:50:55 UTC  
> Closed at: 2023-02-12 18:47:02 UTC  
> Url: https://github.com/boostorg/function/issues/45  

The following test program:  
```  
#include <boost/signals2/signal.hpp>  
#include <boost/bind/bind.hpp>  
  
void ConnectedFunc(void)  
{  
}  
  
int main()  
{  
    using namespace boost::placeholders;  
  
    boost::signals2::signal<void()> sig;  
  
    // connect  
    sig.connect(boost::bind(&ConnectedFunc));  
  
    // disconnect  
    sig.disconnect(boost::bind(&ConnectedFunc));  
}  
```  
Compiles fine with `g++ -std=c++17 main.cc`, but fails with `g++ -std=c++20 main.cc` at the site of the disconnect call.  
  
The problem seems to be related to the `operator==` for the type returned by boost::bind (error from gcc 10, but a similar error is reported by other versions of gcc and clang):  
  
```  
<snip>  
/usr/include/boost/signals2/detail/signal_template.hpp:530:46: error: return type of ‘boost::_bi::bind_t<bool, boost::_bi::equal, boost::_bi::list2<boost::_bi::bind_t<R, F, L>, typename boost::_bi::add_value<A4>::type> > boost::_bi::operator==(const boost::_bi::bind_t<R, F, L>&, A2) [with R = void; F = void (*)(); L = boost::_bi::list0; A2 = boost::function<void()>; typename boost::_bi::add_value<A4>::type = boost::_bi::value<boost::function<void()> >]’ is not ‘bool’  
/usr/include/boost/signals2/detail/signal_template.hpp:530:46: note: used as rewritten candidate for comparison of ‘boost::signals2::slot<void(), boost::function<void()> >::slot_function_type’ {aka ‘boost::function<void()>’} and ‘const boost::_bi::bind_t<void, void (*)(), boost::_bi::list0>’  
```  
  
I'm not sure if this is a bug with the signals2 library, a bug with the bind library, or whether this form of disconnect is no longer supported and the documentation just doesn't make it clear, but since the point at which I encountered it was in using the signals2 library I'm reporting it here first.

---

## Comment 1

> Username: fmhess  
> Created at: 2023-02-10 17:51:06 UTC  
> Url: https://github.com/boostorg/function/issues/45#issuecomment-1426912540  

I think it is a bind issue.  Anyways, I can reproduce without signals2:  
  
```  
#include <boost/bind/bind.hpp>  
#include <boost/function.hpp>  
#include <iostream>  
  
void ConnectedFunc(void)  
{  
}  
  
int main()  
{  
	boost::function<void()> f = boost::bind(&ConnectedFunc);  
	auto g = boost::bind(&ConnectedFunc);  
	if (f == g) std::cout << "equal" << std::endl;  
	else std::cout << "not equal" << std::endl;  
}  
  
```

---

## Comment 2

> Username: richmills3  
> Created at: 2023-02-11 09:42:13 UTC  
> Url: https://github.com/boostorg/function/issues/45#issuecomment-1426912541  

Yes, I'm quite willing to believe it's actually a bind issue. I've worked around the problem in our code so feel free to close this if you're happy there's nothing that needs fixing in signals2.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-02-11 16:27:44 UTC  
> Url: https://github.com/boostorg/function/issues/45#issuecomment-1426912542  

It's not a Bind issue.  
  
The problem here is that Function and Bind want `f == g` to mean different things. Function wants it to mean `f.contains(g)`, and Bind constructs a lambda expression that returns `ConnectedFunc() == f`.  
  
This is ordinarily not a conflict because both libraries only define `operator==` when their own types are on the left hand side. So in this example, `f == g` uses the `operator==` from Function because `f` is `boost::function`.  
  
However, C++20 has its own opinions on how `operator==` has to be defined (thanks to people on the committee who know better than us), so equality has to be symmetric, and it's no longer possible for two libraries to define two incompatible, but not conflicting, definitions of `operator==`. (C++20 is the gift that keeps giving.)  
  
This is not something that is possible to fix in either Bind or Function without breaking all uses of the corresponding `operator==`. It can only be fixed in Signals2, by not using `operator==` for testing `contains` when the slot function is `boost::function`.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-02-11 16:44:51 UTC  
> Url: https://github.com/boostorg/function/issues/45#issuecomment-1426912543  

On second thought, this might be possible to fix on the Function side.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-02-12 18:50:55 UTC  
> Url: https://github.com/boostorg/function/issues/45#issuecomment-1427105028  

This specific issue is now fixed, but the original program still fails to compile under C++20, for other (similar) reasons that can't be addressed on the Function side.  
  
There's a change in C++23 that makes it compile, though.  
  
https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2468r2.html
