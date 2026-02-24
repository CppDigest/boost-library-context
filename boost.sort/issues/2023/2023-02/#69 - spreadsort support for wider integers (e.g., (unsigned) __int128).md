# #69 - spreadsort support for wider integers (e.g., (unsigned) __int128) [Closed]

> Username: thompsonbry  
> Created at: 2023-02-08 19:14:17 UTC  
> Updated at: 2023-02-09 00:49:23 UTC  
> Closed at: 2023-02-08 23:45:03 UTC  
> Url: https://github.com/boostorg/sort/issues/69  

Hello, I am curious whether spreadsort is known to (or thought to) work with wider integer types using an appropriately written rightshift() method.

---

## Comment 1

> Username: thompsonbry  
> Created at: 2023-02-08 23:44:59 UTC  
> Url: https://github.com/boostorg/sort/issues/69#issuecomment-1423384544  

Update: seems to work :-)

---

## Comment 2

> Username: spreadsort  
> Created at: 2023-02-09 00:39:16 UTC  
> Url: https://github.com/boostorg/sort/issues/69#issuecomment-1423433366  

Yes.  It's important that the return type from rightshift be able to hold  
the full potential range of numbers and be subtracted from another of its  
type (basically act like an int of sufficient size).  As long as you've got  
that and a comparison operator it should work fine.  
  
Note that if your number is truly huge you might want to use string_sort to  
avoid creating a huge data type on the stack every time you do a  
"rightshift":  
https://www.boost.org/doc/libs/1_81_0/libs/sort/doc/html/sort/single_thread/spreadsort/sort_hpp/string_sort.html,  
but I'd be surprised if that did as well as integer_sort for something as  
small as 128 bits.  
Your rightshift functor should also be very cheap just like a comparison  
operator.  
  
On Wed, Feb 8, 2023 at 6:45 PM Bryan Thompson ***@***.***>  
wrote:  
  
> Closed #69 <https://github.com/boostorg/sort/issues/69> as completed.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/69#event-8474059269>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXGIA6HS5WRSN4OIFPTWWQVYTANCNFSM6AAAAAAUVUVR3Y>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 3

> Username: spreadsort  
> Created at: 2023-02-09 00:49:23 UTC  
> Url: https://github.com/boostorg/sort/issues/69#issuecomment-1423448844  

On Wed, Feb 8, 2023 at 7:38 PM Steven Ross ***@***.***> wrote:  
  
> Yes.  It's important that the return type from rightshift be able to hold  
> the full potential range of numbers and be subtracted from another of its  
> type (basically act like an int of sufficient size).  As long as you've got  
> that and a comparison operator it should work fine.  
>  
  
It'll fail to compile if the number is larger than boost::uintmax_t, in  
which case you'd have to use string_sort as noted below. There may be  
platforms on which 128 bits is bigger than boost::uintmax_t.  
  
  
> Note that if your number is truly huge you might want to use string_sort  
> to avoid creating a huge data type on the stack every time you do a  
> "rightshift":  
> https://www.boost.org/doc/libs/1_81_0/libs/sort/doc/html/sort/single_thread/spreadsort/sort_hpp/string_sort.html,  
> but I'd be surprised if that did as well as integer_sort for something as  
> small as 128 bits.  
> Your rightshift functor should also be very cheap just like a comparison  
> operator.  
>  
  
  
>  
> On Wed, Feb 8, 2023 at 6:45 PM Bryan Thompson ***@***.***>  
> wrote:  
>  
>> Closed #69 <https://github.com/boostorg/sort/issues/69> as completed.  
>>  
>> —  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/sort/issues/69#event-8474059269>, or  
>> unsubscribe  
>> <https://github.com/notifications/unsubscribe-auth/ABG2HXGIA6HS5WRSN4OIFPTWWQVYTANCNFSM6AAAAAAUVUVR3Y>  
>> .  
>> You are receiving this because you are subscribed to this thread.Message  
>> ID: ***@***.***>  
>>  
>
