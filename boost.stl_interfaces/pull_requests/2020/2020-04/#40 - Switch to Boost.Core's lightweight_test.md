# #40 Switch to Boost.Core's lightweight_test [Merged]

> Username: glenfe  
> Created at: 2020-04-30 19:53:58 UTC  
> Updated at: 2020-05-02 02:50:39 UTC  
> Merged at: 2020-05-02 02:50:04 UTC  
> Closed at: 2020-05-02 02:50:04 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/40  

The dependencies for testing decrease significantly (as shown in the `cmake/dependencies.cmake` diff) as does the time to build and run the tests.

---

## Comment 1

> Username: glenfe  
> Created_at: 2020-05-01 20:41:23 UTC  
> Updated_at: 2020-05-01 21:37:50 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/40#issuecomment-622559408  

@tzlaine Looks like on OSX the tests are using the system installed Boost which doesn't have newer facilities in <boost/core/lightweight_test.hpp>, while on Linux they're cloning Boost from Github. I've updated the `find_package()` requirement from Boost 1.64.0 to Boost 1.74.0.

---

## Comment 2

> Username: tzlaine  
> Created_at: 2020-05-01 23:25:20 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/40#issuecomment-622609286  

No objection.  I probably should have been doing that all along.  
  
Zach  
  
On Fri, May 1, 2020 at 3:41 PM Glen Fernandes <notifications@github.com>  
wrote:  
  
> @tzlaine <https://github.com/tzlaine> Looks like on OSX the tests are  
> using the system installed Boost which doesn't have newer facilities in  
> <boost/core/lightweight_test.hpp>, while on Linux they're cloning Boost  
> from Github. Any objection to making the OSX tests do the same?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/stl_interfaces/pull/40#issuecomment-622559408>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAAUCANZ3DIFZSMG42KA6ZLRPMXYBANCNFSM4MWQTMOQ>  
> .  
>

---

## Comment 3

> Username: glenfe  
> Created_at: 2020-05-02 01:04:41 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/40#issuecomment-622646176  

@tzlaine Done.

---

## Comment 4

> Username: tzlaine  
> Created_at: 2020-05-02 02:50:39 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/40#issuecomment-622658323  

Thanks, Glen!  
  
Zach  
  
On Fri, May 1, 2020 at 8:04 PM Glen Fernandes <notifications@github.com> wrote:  
>  
> @tzlaine Done.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub, or unsubscribe.

---
