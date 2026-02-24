# #83 Fix -Wdeprecated-copy warning with gcc 9. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2019-04-17 14:26:49 UTC  
> Updated at: 2020-06-14 22:57:18 UTC  
> Closed at: 2020-06-14 22:57:18 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83  



---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2019-04-17 15:23:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/phoenix/pull/83#pullrequestreview-227827016  

📁 include/boost/phoenix/core/actor.hpp

```diff
 144 |             expr_type;
 145 | 
 146 |+         BOOST_DEFAULTED_FUNCTION(actor() BOOST_NOEXCEPT, {});
```

> Username: Kojoley  
> Created_at: 2019-04-17 15:23:29 UTC  
> Updated_at: 2019-05-08 16:48:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r276298785  

This breaks placeholders podness on C++03.

> Username: Romain-Geissler-1A  
> Created_at: 2019-04-17 15:27:15 UTC  
> Updated_at: 2019-05-08 16:48:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r276300731  

> This breaks placeholders podness on C++03.  
  
Do you prefer I use some `#ifndef BOOST_NO_CXX11_DEFAULTED_FUNCTIONS` instead ?

> Username: Kojoley  
> Created_at: 2019-04-17 15:29:43 UTC  
> Updated_at: 2019-05-08 16:48:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r276301933  

I do not know. I am not a maintainer of the library. I just pointed that the change brings more trouble than it solves.

> Username: Flast  
> Created_at: 2019-05-08 09:05:34 UTC  
> Updated_at: 2019-05-08 16:48:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r281978156  

I think those should be trivial as much as possible.

> Username: Romain-Geissler-1A  
> Created_at: 2019-05-08 16:49:52 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282154523  

I have updated the code, it now uses `#ifndef BOOST_NO_CXX11_DEFAULTED_FUNCTIONS`


---

## Review 2 [Commented]

> Username: Kojoley  
> Created_at: 2019-05-08 17:00:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/phoenix/pull/83#pullrequestreview-235174904  

📁 include/boost/phoenix/core/actor.hpp

```diff
 146 |+ #ifndef BOOST_NO_CXX11_DEFAULTED_FUNCTIONS
 147 |+         actor() = default;
 148 |+         actor(const actor&) = default;
```

> Username: Kojoley  
> Created_at: 2019-05-08 17:00:12 UTC  
> Updated_at: 2019-05-08 17:00:13 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282158352  

Doing this disables implicit move constructor/assignment, is not it?

> Username: Romain-Geissler-1A  
> Created_at: 2019-05-08 17:07:48 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282161030  

BOOST_PROTO_EXTENDS_ASSIGN defines a copy assignment (which is why I get this error initially in gcc 9). So I think that today there is no implicit move constructor/assignments.

> Username: Kojoley  
> Created_at: 2019-05-08 17:18:03 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282164715  

True. This should be a bug...

> Username: djowel  
> Created_at: 2019-05-08 23:11:41 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282282429  

To be honest, I am not sure how to deal with this. Is there a way to make a minimal test code so we can ask the proto maintainer?

> Username: Kojoley  
> Created_at: 2019-05-08 23:27:11 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282292357  

What you want to ask the proto maintainer about? The only thing on the proto side is why `BOOST_PROTO_EXTENDS_ASSIGN` macro adds a copy assignment operator. The Phoenix probably needs only a template assignment operator.

> Username: djowel  
> Created_at: 2019-05-08 23:30:16 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282292896  

> why `BOOST_PROTO_EXTENDS_ASSIGN` macro adds a copy assignment operator  
  
That one ^. Isn't that the issue here that we are trying to fix in the client(phoenix) side?

> Username: Kojoley  
> Created_at: 2019-05-08 23:32:38 UTC  
> Updated_at: 2019-05-08 23:32:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282293331  

It is the root cause of the warning, yes.

> Username: djowel  
> Created_at: 2019-05-08 23:36:16 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282294041  

> It is the root cause of the warning, yes.  
  
So we're probably "fixing" the wrong side.

> Username: Kojoley  
> Created_at: 2019-05-08 23:39:10 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282294564  

It depends on where the self-assignment of Phoenix placeholders is allowed or not.

> Username: Kojoley  
> Created_at: 2019-05-08 23:40:32 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r282294799  

And I think it cannot be disallowed because the signature of two actors could be the same, but the content is different.

> Username: Kojoley  
> Created_at: 2020-05-04 01:30:06 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r419186144  

It turns out that I have changed the semantics in https://github.com/boostorg/phoenix/commit/1e66ae741f10d185863d7d8d8a54d562efe5144e#diff-85e553336a9d92ba0afb347581e0f32eL207-L212. By removing the copy assignment operator that comes from `BOOST_PROTO_EXTENDS_ASSIGN` we will revert to the previous behavior, but in this case we will again break:  
```cpp  
#include <boost/phoenix.hpp>  
#include <iostream>  
  
int main()  
{  
  using namespace boost::phoenix;  
  
  int i = 0, j = 123;  
  auto ri = ref(i);   
  (ri = ref(j))();  
  std::cout << "i=" << i << ", j=" << j << "\n";  
}  
```  
I did not find a ticket that it was broken before, and I checked that the test suit does not cover this usage, but it looks reasonable not break it again.

> Username: Romain-Geissler-1A  
> Created_at: 2020-05-04 10:49:10 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r419352294  

So I am not sure I am following what shall we do now to silence this warning.

> Username: Romain-Geissler-1A  
> Created_at: 2020-06-08 10:16:30 UTC  
> Updated_at: 2020-06-08 10:16:31 UTC  
> Url: https://github.com/boostorg/phoenix/pull/83#discussion_r436593280  

So, I don't follow here. Is there still a problem to be solved now on the development branch ? If not, I will just close this pull request.


---
