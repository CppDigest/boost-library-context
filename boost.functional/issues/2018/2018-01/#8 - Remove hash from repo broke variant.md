# #8 - Remove hash from repo broke variant [Closed]

> Username: matbech  
> Created at: 2018-01-02 01:11:48 UTC  
> Updated at: 2018-01-02 13:02:30 UTC  
> Closed at: 2018-01-02 10:20:16 UTC  
> Url: https://github.com/boostorg/functional/issues/8  

The commit Remove hash from repo https://github.com/boostorg/functional/commit/16a4ab6de637ba4bd6b8a592a5d13623341d015c broke boost's variant.  
boost variant includes the removed:  
#include <boost/functional/hash_fwd.hpp>  
https://github.com/boostorg/variant/blob/develop/include/boost/variant/detail/hash_variant.hpp#L24

---

## Comment 1

> Username: danieljames  
> Created at: 2018-01-02 10:20:16 UTC  
> Url: https://github.com/boostorg/functional/issues/8#issuecomment-354736386  

It's in the new container_hash module, if you use `git submodule update --init` to update the super project, it should be automatically checked out.

---

## Comment 2

> Username: matbech  
> Created at: 2018-01-02 13:02:30 UTC  
> Url: https://github.com/boostorg/functional/issues/8#issuecomment-354761997  

Thank you. It's working now.  
  
On Tue, Jan 2, 2018 at 11:20 AM, Daniel James <notifications@github.com>  
wrote:  
  
> It's in the new container_hash module, if you use git submodule update  
> --init to update the super project, it should be automatically checked  
> out.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/functional/issues/8#issuecomment-354736386>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AGVZvcunP2gVChJ3l0b3nXrd9kYlaAC4ks5tGgLggaJpZM4RQOdk>  
> .  
>
