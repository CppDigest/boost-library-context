# #13 Lambda Visitor [Closed]

> Username: klemens-morgenstern  
> Created at: 2015-04-04 01:18:08 UTC  
> Updated at: 2016-08-24 17:51:52 UTC  
> Closed at: 2016-08-24 17:51:52 UTC  
> Url: https://github.com/boostorg/variant/pull/13  

This is a little utility I built for one of my projects and I thought it might be interesting for the library. It allows a very short syntax to get the value of a variant by using lambdas. That could look like:  
  
`variant<int, string> var;  
auto vis = make_lambda_visitor([](int){...}, [](string){...});  
var.apply_visitor(vis);`  
  
The whole thing works and though it is names lambda-visitor it should work with all functors.  
Since it has to deduce the return type of the call, it cannot use generic lambdas, even if that would be nice. That could be added, by deducing the return type from the first lambda so that a look-up does not happen for the latter ones. Atm all lambdas are checked by static_assert if the return types fit.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2015-04-04 01:19:09 UTC  
> Url: https://github.com/boostorg/variant/pull/13#issuecomment-89478861  

Just FYI: the first version, is the one I use in my other project and I didn't realize how simple lambda-inheritance would make the whole thing. So the older versions are quite bloated.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2015-04-04 11:39:37 UTC  
> Url: https://github.com/boostorg/variant/pull/13#issuecomment-89554140  

Ok, a solution for generic lambdas is now implemented, which can be done by explicitly giving a return type, which looks as used in test/lambda_test4.cpp.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2015-04-25 07:31:39 UTC  
> Url: https://github.com/boostorg/variant/pull/13#issuecomment-96145815  

This seems like a good feature. It is more generic than just a visitor for variant so it must be put in a separate utility library.  
  
There were discussions about such addition [here](http://boost.2283326.n4.nabble.com/A-more-convenient-Variant-visitation-syntax-td4557364.html) and [here](http://lists.boost.org/Archives/boost/2015/01/218839.php). Unfortunately no such library was submitted for Boost review since then.  
  
If you''ll make a separate library from this patch and submit it for review ([details](http://www.boost.org/community/reviews.html)) I'll help you with the review process and will become the review manager for this library.  
  
PS: Sorry for the long delay

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2015-04-26 17:49:13 UTC  
> Url: https://github.com/boostorg/variant/pull/13#issuecomment-96413140  

So what you're saying is, that a library should be developed to make creating visitors easy and this library could then be used in boost/variant?  
That certainly would be nice, I'll think of a design for that.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2015-11-19 19:43:07 UTC  
> Url: https://github.com/boostorg/variant/pull/13#issuecomment-158172819  

There's some implementation for that feature that you may wish to check out:  
https://github.com/viboes/tags/blob/master/include/yafpl/v1/functional/overload.hpp

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2016-08-24 17:51:52 UTC  
> Url: https://github.com/boostorg/variant/pull/13#issuecomment-242152314  

I think to provide the visit as a member-function of boost::variant is the better solution as in [std::variant](http://en.cppreference.com/w/cpp/utility/variant/visit). So I think this PR would be obsolete.

---
