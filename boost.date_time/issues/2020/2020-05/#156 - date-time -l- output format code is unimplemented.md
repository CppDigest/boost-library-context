# #156 - date-time "l" output format code is unimplemented [Open]

> Username: mhemmer53  
> Created at: 2020-05-28 17:41:27 UTC  
> Updated at: 2020-06-04 15:19:39 UTC  
> Url: https://github.com/boostorg/date_time/issues/156  

Boost 1.72.0, VS 2019, Windows 10, toolset v142  
"l" is not recognized and appears unimplemented, attempted use causes abort to be called, no exception is thrown, suggested fix attached  
[time_facet.zip](https://github.com/boostorg/date_time/files/4697327/time_facet.zip)

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-06-01 14:32:18 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-636894279  

It isn't documented to be either -- but it's a valid request.    
  
https://www.boost.org/doc/libs/1_73_0/doc/html/date_time/date_time_io.html#date_time.format_flags

---

## Comment 2

> Username: JeffGarland  
> Created at: 2020-06-01 14:33:02 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-636894649  

is is possible to get and unzip upload -- wary of opening those for security reasons

---

## Comment 3

> Username: mhemmer53  
> Created at: 2020-06-02 12:02:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-637495460  

I see %l documented for output, I fact it causes my program to abort.  
  
From: Jeff Garland <notifications@github.com>  
Sent: Monday, June 1, 2020 09:33  
To: boostorg/date_time <date_time@noreply.github.com>  
Cc: Mike Hemmer <mhemmer@functionaldata.com>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/date_time] date-time "l" output format code is unimplemented (#156)  
  
  
It isn't documented to be either -- but it's a valid request.  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/date_time/date_time_io.html#date_time.format_flags  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/date_time/issues/156#issuecomment-636894279>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/APYF3CN3WKP3V7DJAXECOQDRUO3YHANCNFSM4NNJG6NQ>.

---

## Comment 4

> Username: mhemmer53  
> Created at: 2020-06-02 12:05:02 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-637496863  

Jeff,  
  
The file I modified is attached.  
  
Thanks,  
  
Mike Hemmer  
  
From: Jeff Garland <notifications@github.com>  
Sent: Monday, June 1, 2020 09:33  
To: boostorg/date_time <date_time@noreply.github.com>  
Cc: Mike Hemmer <mhemmer@functionaldata.com>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/date_time] date-time "l" output format code is unimplemented (#156)  
  
  
is is possible to get and unzip upload -- wary of opening those for security reasons  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/date_time/issues/156#issuecomment-636894649>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/APYF3CM7LDU5WJ3HZLAXMWLRUO323ANCNFSM4NNJG6NQ>.

---

## Comment 5

> Username: k-ross  
> Created at: 2020-06-03 23:02:58 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-638505219  

> It isn't documented to be either -- but it's a valid request.  
>   
> https://www.boost.org/doc/libs/1_73_0/doc/html/date_time/date_time_io.html#date_time.format_flags  
  
Actually it is documented, at the link you provided. Scroll down past the "date facet format flags" to "time facet format flags". It's listed there.

---

## Comment 6

> Username: JeffGarland  
> Created at: 2020-06-04 00:35:48 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-638531452  

Yes I missed it in the docs. If you look at the tests (testtime_input_facet.cpp) it's clear on the input side that it's not implemented (commented out test).  On the output side, nominally the default behavior is to dispatch std::time_put for all the flags -- we only intercept stuff that extends normal c/c++ formatting.  %I is covered in the base set for time_put, so it actually should work. So this maybe a VC problem and not a date_time problem.

---

## Comment 7

> Username: mhemmer53  
> Created at: 2020-06-04 12:25:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-638815159  

Jeff,  
  
I do not see %l documented as a valid put_time format code here: https://docs.w3cub.com/cpp/io/manip/put_time/ Or here: https://en.cppreference.com/w/cpp/io/manip/put_time Or here: http://www.cplusplus.com/reference/iomanip/put_time/  
  
Mike  
  
From: Jeff Garland <notifications@github.com>  
Sent: Wednesday, June 3, 2020 19:36  
To: boostorg/date_time <date_time@noreply.github.com>  
Cc: Mike Hemmer <mhemmer@functionaldata.com>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/date_time] date-time "l" output format code is unimplemented (#156)  
  
  
Yes I missed it in the docs. If you look at the tests (testtime_input_facet.cpp) it's clear on the input side that it's not implemented (commented out test). On the output side, nominally the default behavior is to dispatch std::time_put for all the flags -- we only intercept stuff that extends normal c/c++ formatting. %I is covered in the base set for time_put, so it actually should work. So this maybe a VC problem and not a date_time problem.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/date_time/issues/156#issuecomment-638531452>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/APYF3CMRHCH7MYBWTIYHIBTRU3T7DANCNFSM4NNJG6NQ>.

---

## Comment 8

> Username: JeffGarland  
> Created at: 2020-06-04 13:44:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-638857418  

Look again really close: https://en.cppreference.com/w/cpp/io/manip/put_time -- you'll see this:  
  
I | writes hour as a decimal number, 12 hour clock (range [01,12]) | tm_hour  
-- | -- | --  
  
I've confirmed the following works on linux with gcc9.2.  I'll also note that there's a widestream test of this same code that passes on all the platforms.  Note the wstream test is in an ifdef, but don't think windows would be excluded from it.     
  
        
      ptime t(  date(2020,Oct,13), time_duration(18,01,56));  
      time_facet* timefacet = new time_facet();  
      timefacet->format("%Y-%b-%d %I:%M:%S %p");  
      cout.imbue(std::locale(cout.getloc(), timefacet));  
      cout << "%I test: " << t << endl;  //%I test: 2020-Oct-13 06:01:56 PM  
  
Unfortunately I can't seem to bend godbolt to include boost for vc++  
  
https://godbolt.org/z/bus8ft

---

## Comment 9

> Username: mhemmer53  
> Created at: 2020-06-04 14:07:38 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-638872426  

Jeff,  
  
The format code I’m looking for is lowercase ell, not uppercase eye.  
  
Mike  
  
From: Jeff Garland <notifications@github.com>  
Sent: Thursday, June 4, 2020 08:45  
To: boostorg/date_time <date_time@noreply.github.com>  
Cc: Mike Hemmer <mhemmer@functionaldata.com>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/date_time] date-time "l" output format code is unimplemented (#156)  
  
  
Look again really close: https://en.cppreference.com/w/cpp/io/manip/put_time -- you'll see this:  
I  
writes hour as a decimal number, 12 hour clock (range [01,12])  
tm_hour  
  
I've confirmed the following works on linux with gcc9.2. I'll also note that there's a widestream test of this same code that passes on all the platforms. Note the wstream test is in an ifdef, but don't think windows would be excluded from it.  
  
  ptime t(  date(2020,Oct,13), time_duration(18,01,56));  
  
  time_facet* timefacet = new time_facet();  
  
  timefacet->format("%Y-%b-%d %I:%M:%S %p");  
  
  cout.imbue(std::locale(cout.getloc(), timefacet));  
  
  cout << "%I test: " << t << endl;  //%I test: 2020-Oct-13 06:01:56 PM  
  
Unfortunately I can't seem to bend godbolt to include boost for vc++  
  
https://godbolt.org/z/bus8ft  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/date_time/issues/156#issuecomment-638857418>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/APYF3CL6FJAZOISYMKFVA7DRU6QOHANCNFSM4NNJG6NQ>.

---

## Comment 10

> Username: JeffGarland  
> Created at: 2020-06-04 15:19:39 UTC  
> Url: https://github.com/boostorg/date_time/issues/156#issuecomment-638919965  

ok right -- really sorry for the confusion.  So I looked it up.  The time_put is specified in terms of iso/iec 9945 strptime which does not provide the '%l' (where that's an ell, not an eye).  That said, some platforms are likely using strftime which does support.  So this does work on Linux/gcc.  Should be noted that c++20 std::chrono and std::format is still based on strptime, so this literal won't be available there.   
  
https://godbolt.org/z/oKRcJZ
