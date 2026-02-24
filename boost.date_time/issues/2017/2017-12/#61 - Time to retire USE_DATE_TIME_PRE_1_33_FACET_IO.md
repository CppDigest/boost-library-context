# #61 - Time to retire USE_DATE_TIME_PRE_1_33_FACET_IO [Closed]

> Username: jeking3  
> Created at: 2017-12-27 14:05:30 UTC  
> Updated at: 2020-04-05 18:01:12 UTC  
> Closed at: 2020-04-05 18:01:12 UTC  
> Url: https://github.com/boostorg/date_time/issues/61  

1.33.0 came out a long time ago.  Time to retire the pre-1.33.0 code paths.

---

## Comment 1

> Username: eldiener  
> Created at: 2017-12-31 05:03:31 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-354585556  

I think you should query the developers mailing list regarding this potential removal.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-01-17 18:38:43 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-358400719  

There was no feedback indicating this should not be done.

---

## Comment 3

> Username: eldiener  
> Created at: 2018-01-17 18:52:58 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-358404799  

I will remove it and update the 'develop' branch after I do.

---

## Comment 4

> Username: eldiener  
> Created at: 2018-01-17 19:51:27 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-358423096  

It looks to me that all sorts of tests are only being run when USE_DATE_TIME_PRE_1_33_FACET_IO is being defined. These includes tests for:  
  
- date_time_wide_streaming  
- date_time_pre_133_facet  
- date_time_serialization  
  
I do not see the same tests being run without USE_DATE_TIME_PRE_1_33_FACET_IO being defined. I feel that I am missing something in the bjam file for the tests but I can not see what it is.  
  
This suggests to me that we can not remove the code which contains USE_DATE_TIME_PRE_1_33_FACET_IO.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-01-18 03:36:03 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-358527760  

Hmm, interesting.  Well I suggest we leave this here for now and revisit it once the trac backlog is drained a little more, or someone can pick it up.   
  
It would seem that date_time_pre_133_facet would be unnecessary entirely based on the name alone.. not sure about the others.

---

## Comment 6

> Username: jeking3  
> Created at: 2018-01-21 13:37:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-359249052  

It's possible that serialization integration only happens pre-1.33 because of the streaming and traits introduced in 1.33... While enabling UBSAN builds I found I had to disable the serialization tests which only run in pre-1.33 compatibility mode.  Might be more reason to remove that legacy code...

---

## Comment 7

> Username: eldiener  
> Created at: 2018-01-22 03:43:50 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-359317326  

If serialization needs the pre-1.33 code I do not see how we can remove that code without saying that serialization does not work for iostreams.

---

## Comment 8

> Username: eldiener  
> Created at: 2018-01-22 03:55:24 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-359318479  

Sorry I meant to say:  
  
If serialization needs the pre-1.33 code I do not see how we can remove that code without saying that serialization does not work for date_time.

---

## Comment 9

> Username: jeking3  
> Created at: 2018-01-22 13:22:44 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-359421753  

True, will come back to it eventually and see.

---

## Comment 10

> Username: JeffGarland  
> Created at: 2020-03-14 23:03:35 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-599145623  

So I'm planning to remove support for this in boost 1.73 -- the time has seriously come.  I've made an initial commit that removes the macros the force this mode for long dropped compilers.  I posted a warning on both the mailing lists: crickets.  
  
https://lists.boost.org/boost-users/2020/03/90303.php  
https://lists.boost.org/Archives/boost/2020/03/248334.php  
  
I have a 'dt_nolib' branch which I'm going to put onto develop shortly -- removing the library build.  That changes the testing quite dramatically and as part of that I've dropped all the pre-1.33 tests.  The code is not removed, yet -- but that will be soon.

---

## Comment 11

> Username: JeffGarland  
> Created at: 2020-04-05 18:01:12 UTC  
> Url: https://github.com/boostorg/date_time/issues/61#issuecomment-609457283  

This has been merged to master for release in 1.73
