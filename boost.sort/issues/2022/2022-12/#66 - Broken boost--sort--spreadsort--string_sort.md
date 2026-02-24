# #66 - Broken boost::sort::spreadsort::string_sort [Closed]

> Username: sgn  
> Created at: 2022-12-17 07:30:14 UTC  
> Updated at: 2022-12-17 22:33:09 UTC  
> Closed at: 2022-12-17 22:31:53 UTC  
> Url: https://github.com/boostorg/sort/issues/66  

Below function  
https://github.com/boostorg/sort/blob/db798f62caea5b6e56c98a12daeaf336e4e78561/include/boost/sort/spreadsort/detail/string_sort.hpp#L696-L705  
  
Will be disable if `sizeof(Unsigned_char_type) <= 2`, that means that function will be enabled when `sizeof(Unsigned_char_type) > 2`.  
  
However, when `sizeof(Unsigned_char_type) > 2`, the `BOOST_STATIC_ASSERT( sizeof(Unsigned_char_type) <= 2 );` will run into failure.

---

## Comment 1

> Username: spreadsort  
> Created at: 2022-12-17 14:42:35 UTC  
> Url: https://github.com/boostorg/sort/issues/66#issuecomment-1356291354  

Good point, I missed that in the code review for  
https://github.com/boostorg/sort/pull/40.  I'll remove the unnecessary  
ASSERTs.  
  
On Sat, Dec 17, 2022 at 2:30 AM Đoàn Trần Công Danh <  
***@***.***> wrote:  
  
> Below function  
>  
> https://github.com/boostorg/sort/blob/db798f62caea5b6e56c98a12daeaf336e4e78561/include/boost/sort/spreadsort/detail/string_sort.hpp#L696-L705  
>  
> Will be disable if sizeof(Unsigned_char_type) <= 2, that means that  
> function will be enabled when sizeof(Unsigned_char_type) > 2.  
>  
> However, when sizeof(Unsigned_char_type) > 2, the BOOST_STATIC_ASSERT(  
> sizeof(Unsigned_char_type) <= 2 ); will run into failure.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/66>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXFJYSON72YSFMQ7P73WNVTZDANCNFSM6AAAAAATBZYPVM>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: spreadsort  
> Created at: 2022-12-17 22:31:31 UTC  
> Url: https://github.com/boostorg/sort/issues/66#issuecomment-1356489434  

Thanks for spotting that!  The fix is in develop: https://github.com/boostorg/sort/commit/2e6674d63f2f541a3fd32f50d593be0c71f686f8

---

## Comment 3

> Username: spreadsort  
> Created at: 2022-12-17 22:33:09 UTC  
> Url: https://github.com/boostorg/sort/issues/66#issuecomment-1356489704  

merge into master is here: https://github.com/boostorg/sort/pull/67, waiting on Francisco's review.
