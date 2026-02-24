# #29 Fixed a bug for parsing posix timezone: + on the west from UTC, - on the east [Open]

> Username: zerkms  
> Created at: 2015-10-06 20:34:40 UTC  
> Updated at: 2022-02-28 22:26:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/29  

Currently mistakenly the signs are used the other way around  
  
Fixes:  
- https://svn.boost.org/trac/boost/ticket/4545  
- https://svn.boost.org/trac/boost/ticket/3336  
  
PS: It's a follow-up after the https://github.com/boostorg/date_time/pull/28 which I sent to master

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-10-17 04:31:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-148885108  

The only problem I see with this PR is that it is a breaking change for end-users even if it is the correct calculation. If we make this change we definitely have to alert users of date_time so that they are readily aware of it.

---

## Comment 2

> Username: zerkms  
> Created_at: 2015-10-17 05:21:25 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-148886694  

@eldiener yeah, I've been thinking on this as well: every bugfix always is a breaking change of some sort.

---

## Comment 3

> Username: zerkms  
> Created_at: 2015-11-08 21:36:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-154876150  

So guys, do you need any additional input from me yet? I'm not sure if it's me here blocking it or not.

---

## Comment 4

> Username: mclow  
> Created_at: 2015-11-09 20:05:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-155175912  

I am not comfortable taking this patch at this point in the release cycle. I expect a fair amount of pushback from users of Boost.DateTime.  I'd prefer to postpone this until after the 1.60 release is made.

---

## Comment 5

> Username: zerkms  
> Created_at: 2015-11-09 20:20:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-155181236  

> I expect a fair amount of pushback from users of Boost.DateTime  
  
How this would happen though? The bug was there for at least 6 (six) years without literally any feedback. What would draw attention after 1.60 is released?

---

## Comment 6

> Username: mclow  
> Created_at: 2015-11-09 20:25:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-155182571  

Sadly, ISO 8601 says one thing:  
  
> A time zone offset of "+hh:mm" indicates that the date/time uses a local time zone which is "hh" hours and "mm" minutes ahead of UTC. A time zone offset of "-hh:mm" indicates that the date/time uses a local time zone which is "hh" hours and "mm" minutes behind UTC.  
  
while POSIX says the opposite:  
  
> offset Indicates the value added to the local time to arrive at Coordinated Universal Time.

---

## Comment 7

> Username: mclow  
> Created_at: 2015-11-09 20:27:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-155182965  

I believe a lot of people who use Boost.DateTime are used to the current behavior; and would be unpleasantly surprised if their timestamps changed from +XXXX to -XXXX.

---

## Comment 8

> Username: mclow  
> Created_at: 2015-12-18 16:33:45 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-165827742  

Now that Boost 1.60 has shipped, I would like to resolve this.  
  
Just an FYI; I spoke to Jeff Garland, the author of Boost.DateTime, and his response was "change the documentation, not the code".

---

## Comment 9

> Username: zerkms  
> Created_at: 2015-12-18 21:13:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-165898955  

Which means there will be no way to parse timezone strings in boost. Okay.

---

## Comment 10

> Username: zerkms  
> Created_at: 2015-12-18 22:26:16 UTC  
> Updated_at: 2015-12-18 22:30:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-165911114  

Btw, I checked the code once again: even if you fix the documentation the class name  
  
```  
posix_time_zone  
```  
  
still will be misleading, since it does not represent a POSIX timezone.  
  
The same is true for `to_posix_string` method, which does not serialize an object to a POSIX string.  
  
So, whatever you specify in a documentation - the class and the method names are incorrect. But curious to see how this could be explained in the documentation to not make it even worse than it is now.  
  
Not to say that there is no way to integrate boost with tools and other software that implements the standard properly.

---

## Comment 11

> Username: Lastique  
> Created_at: 2016-11-30 16:56:45 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-263928960  

@mclow Perhaps we could make this change conditional on a config macro, disabled by default + a note in release notes. After N releases we could switch the default to the new behavior and have the old behavior conditionally enabled - with another note in release notes.  
  
I'm not comfortable with leaving bugs solely on the purpose of backward compatibility. Bugs have to be fixed (tm).

---

## Comment 12

> Username: eldiener  
> Created_at: 2016-12-01 13:33:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-264173955  

I agree that this change should be made based on a macro, as specified by mclow. I think the macro should be BOOST_DATE_TIME_SOMETHING... as a date/time config macro. I think we must wait until after the 1.63 is out but that we should work toward this change and can make it in 'develop' now. This merge should not be made unconditionally but forms the basis for the macro-based change. Since I am sort of the macro guy, among Boost developers I am comfortable with making this change and merging it to 'develop'.

---

## Comment 13

> Username: zerkms  
> Created_at: 2016-12-01 19:15:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-264265528  

@eldiener any chance you keep my contribution so that I at least had left any trace in the project? :-)

---

## Comment 14

> Username: Lastique  
> Created_at: 2016-12-01 19:23:01 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-264267651  

There's an ongoing discussion on the boost-maint list regarding this PR. A slightly different solution has been proposed (not involving a config macro).

---

## Comment 15

> Username: jeking3  
> Created_at: 2017-12-21 14:10:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-353360028  

It looks like this stalled or was not closed off (per the discussion on the mailing list) - was there a resolution?

---

## Comment 16

> Username: Lastique  
> Created_at: 2017-12-21 15:30:50 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-353379836  

To my knowledge, the problem is not fixed.  
  
The discussion in the boost-maint list came to a possible solution involving:  
  
- Adding a new function instead of `to_posix_string`, that can produce a POSIX or ISO string based on an argument passed by user (e.g. an enum).  
- Deprecating `to_posix_string`. After a few releases it can be removed.  
  
I think the idea was generally agreed upon but noone took the time to implement it.

---

## Comment 17

> Username: jeking3  
> Created_at: 2018-01-18 03:37:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-358527950  

Okay we'll leave this here for now as a placeholder for a backlog item.

---

## Comment 18

> Username: jeking3  
> Created_at: 2022-02-28 22:26:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/29#issuecomment-1054723222  

It is okay to have a breaking change as long as:  
  
1. It is noted in the release notes.  
2. There is a way to enable the old behavior, so it is not breaking.  
  
This is typically used when a library is producing incorrect results, such as this.  
It looks like there was a wider discussion but nothing much has happened for a few years on this.  
I am tickling it to see what happens.  At the very least it should be rebased on develop so it can run lots of CI.

---
