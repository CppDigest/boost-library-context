# #52 - use of "id" as variable name causes objective-c compiler problems [Closed]

> Username: LostVector  
> Created at: 2020-01-18 01:22:08 UTC  
> Updated at: 2020-01-18 22:44:09 UTC  
> Closed at: 2020-01-18 22:43:47 UTC  
> Url: https://github.com/boostorg/system/issues/52  

"id" is a reserved keyword in objective-c / c++.  It is used as a variable name in the following header/function:  
  
https://github.com/boostorg/system/blob/develop/include/boost/system/error_code.hpp#L782-L807  
  
It appears some objective-c compilers can deal with this, but also some clearly can not.  
  
I ran across this issue because I was using boost asio, and at some point between 1.65 and 1.72 it included the aforementioned header, which caused compilation to break.  
  
Patching the variable name to "cat_id" fixes the issue.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-01-18 14:42:24 UTC  
> Url: https://github.com/boostorg/system/issues/52#issuecomment-575904663  

What specific errors did you get?

---

## Comment 2

> Username: LostVector  
> Created at: 2020-01-18 19:51:12 UTC  
> Url: https://github.com/boostorg/system/issues/52#issuecomment-575932824  

Expected unqualified-id before ‘=‘ at line 790 in error_code.hpp.  
  
Get Outlook for iOS<https://aka.ms/o0ukef>  
  
________________________________  
From: Peter Dimov <notifications@github.com>  
Sent: Saturday, January 18, 2020 6:42 AM  
To: boostorg/system  
Cc: LostVector; Author  
Subject: Re: [boostorg/system] use of "id" as variable name causes objective-c compiler problems (#52)  
  
  
What specific errors did you get?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/system/issues/52?email_source=notifications&email_token=ABWQSG5TW2WHJ5XJ35HAHDDQ6MINDA5CNFSM4KIQHFY2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEJJZXFY#issuecomment-575904663>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/ABWQSGYG6UAGSCIUZPNJBI3Q6MINDANCNFSM4KIQHFYQ>.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-01-18 22:44:09 UTC  
> Url: https://github.com/boostorg/system/issues/52#issuecomment-575945014  

Should be fixed now. Thanks for the report.
