# #2134 - C4003: not enough arguments for function-like macro invocation 'max' in boost version 1.72.0 [Closed]

> Username: DV-Developer  
> Created at: 2020-12-03 22:03:00 UTC  
> Updated at: 2022-01-09 05:17:04 UTC  
> Closed at: 2022-01-09 05:17:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2134  

Updated to version 1.72.0 and am  getting the warning indicated in the title along with associated errors for the file: file_stdio.ipp. The call std::numeric_limits<long>::max() is missing parenthesis to prevent windef.h max macro insertion.  
  
Full warning and errors:  
boost.1.72.0.0\lib\native\include\boost\beast\core\impl\file_stdio.ipp(244,71): warning C4003: not enough arguments for function-like macro invocation 'max'  
boost.1.72.0.0\lib\native\include\boost\beast\core\impl\file_stdio.ipp(244,71): error C2589: '(': illegal token on right side of '::'  
boost.1.72.0.0\lib\native\include\boost\beast\core\impl\file_stdio.ipp(244,7): error C2062: type 'unknown-type' unexpected  
boost.1.72.0.0\lib\native\include\boost\beast\core\impl\file_stdio.ipp(244): error C2144: syntax error: 'unknown-type' should be preceded by '('  
boost.1.72.0.0\lib\native\include\boost\beast\core\impl\file_stdio.ipp(244,77): error C2059: syntax error: ')'  
boost.1.72.0.0\lib\native\include\boost\beast\core\impl\file_stdio.ipp(245,5): error C2143: syntax error: missing ';' before '{'

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-12-03 23:02:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2134#issuecomment-738425405  

is this fixed in the latest Boost?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-12-04 05:03:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2134#issuecomment-738566478  

I seem to remember fixing a problem like this.

---

## Comment 3

> Username: DV-Developer  
> Created at: 2020-12-04 20:21:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2134#issuecomment-738998830  

It was logged as a problem and reported fixed for 1.73 in issue 1980 I believe. I’m not seeing 1.73 using Nuget through VS though. So I’m using 1.72.  
  
   
  
Thanks,  
  
Chris  
  
   
  
From: Vinnie Falco <notifications@github.com>   
Sent: Thursday, December 03, 2020 6:03 PM  
To: boostorg/beast <beast@noreply.github.com>  
Cc: Fike, Christopher Allen (Chris H45) CIV USN NAVSURFWARCEN DAH VA (USA) <christopher.fike@navy.mil>; Author <author@noreply.github.com>  
Subject: [Non-DoD Source] Re: [boostorg/beast] C4003: not enough arguments for function-like macro invocation 'max' in boost version 1.72.0 (#2134)  
  
   
  
is this fixed in the latest Boost?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub <https://no-click.mil/?https://github.com/boostorg/beast/issues/2134#issuecomment-738425405> , or unsubscribe <https://no-click.mil/?https://github.com/notifications/unsubscribe-auth/AMO6T3L3A2U5QIP5LX67GG3STAKKXANCNFSM4UMTDDVA> .  <https://no-click.mil/?https://github.com/notifications/beacon/AMO6T3KN4NRKGSKVXGIFIETSTAKKXA5CNFSM4UMTDDVKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOFQBXUPI.gif>

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2134#issuecomment-850857941  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2134#issuecomment-1008232333  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
