# #21 - warning: comparing floating point with == or != is unsafe [-Wfloat-equal] [Closed]

> Username: jhasse  
> Created at: 2019-12-11 13:52:03 UTC  
> Updated at: 2020-02-18 03:04:07 UTC  
> Closed at: 2020-02-18 03:04:07 UTC  
> Url: https://github.com/boostorg/qvm/issues/21  

Using `boost::qvm::normalize` results in the following warning:  
  
```  
boost/qvm/gen/vec_operations2.hpp:564:19: warning: comparing floating point with == or != is unsafe [-Wfloat-equal]  
            if( m2==scalar_traits<typename vec_traits<A>::scalar_type>::value(0) )  
                ~~^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
Is there a way to avoid this?

---

## Comment 1

> Username: zajo  
> Created at: 2019-12-11 20:17:25 UTC  
> Url: https://github.com/boostorg/qvm/issues/21#issuecomment-564714635  

What command line? Which compiler?  
  
Perhaps I can remove the check and leave it up to the FP math to deal with  
the result, though in my experience this error check has been helpful. Such  
perfect zeros tend to be actual errors as opposed to the result of some  
expression. Also, this is a generic function, we don't necessarily know it  
works with floating point numbers.  
  
On Wed, Dec 11, 2019 at 5:52 AM Jan Niklas Hasse <notifications@github.com>  
wrote:  
  
> Using boost::qvm::normalize results in the following warning:  
>  
> boost/qvm/gen/vec_operations2.hpp:564:19: warning: comparing floating point with == or != is unsafe [-Wfloat-equal]  
>             if( m2==scalar_traits<typename vec_traits<A>::scalar_type>::value(0) )  
>                 ~~^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
>  
> Is there a way to avoid this?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/qvm/issues/21?email_source=notifications&email_token=AAEUZWRFTAW26I6US67SPADQYDWAHA5CNFSM4JZPJMQKYY3PNVWWK3TUL52HS4DFUVEXG43VMWVGG33NNVSW45C7NFSM4H7YSECA>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAEUZWRG6N7YJ42F6III7JDQYDWAHANCNFSM4JZPJMQA>  
> .  
>

---

## Comment 2

> Username: jhasse  
> Created at: 2019-12-16 09:57:24 UTC  
> Url: https://github.com/boostorg/qvm/issues/21#issuecomment-565988510  

`Apple clang version 11.0.0`  
  
Boost.Test used the following trick to avoid the warning when checking for 0: https://github.com/boostorg/test/commit/2534a4d65307295d3c421d2ac9e0f5b9fe35271a

---

## Comment 3

> Username: zajo  
> Created at: 2019-12-16 18:59:16 UTC  
> Url: https://github.com/boostorg/qvm/issues/21#issuecomment-566195243  

What command line are you passing to clang? I don't think it is a good idea to compare for a small epsilon, first this adds a call to abs, second, the point of the check is specifically to detect zero (see my previous comment).

---

## Comment 4

> Username: jhasse  
> Created at: 2020-01-24 16:10:11 UTC  
> Updated at: 2020-01-24 16:10:36 UTC  
> Url: https://github.com/boostorg/qvm/issues/21#issuecomment-578194619  

I'm passing `-Wfloat-equal`.  
  
> I don't think it is a good idea to compare for a small epsilon, first this adds a call to abs, second, the point of the check is specifically to detect zero (see my previous comment).  
  
Good point. What do you think about using `std::fpclassify(x) == FP_ZERO` instead?  
  
I've made a small test which seems to work as expected:  
```cpp  
#include <cmath>  
#include <iostream>  
#include <limits>  
  
int main() {  
	for (float x : {-1.f, 0.0001f, std::numeric_limits<float>::epsilon(),  
	                -0.f, +0.f, 0.f }) {  
		std::cout << (std::fpclassify(x) == FP_ZERO) << std::endl;  
	}  
}  
```

---

## Comment 5

> Username: zajo  
> Created at: 2020-01-24 21:30:58 UTC  
> Url: https://github.com/boostorg/qvm/issues/21#issuecomment-578309763  

The issue is that it is valid for the scalar type to not be a floating point type. It would require a wrapper, but I don't see the point. The comparison to zero is correct. I suggest disabling the warning.

---

## Comment 6

> Username: jhasse  
> Created at: 2020-01-27 09:51:07 UTC  
> Url: https://github.com/boostorg/qvm/issues/21#issuecomment-578670483  

I see. I just found this bug report: https://bugs.llvm.org/show_bug.cgi?id=23717  
Apparently clang only warns when doing `f == 0`, not when doing `f == 0.f`.
