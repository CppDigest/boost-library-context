# #63 trac-11142: fix boost::date_time::period_formatter::delimiter_strings and add a unit test for it [Closed]

> Username: jeking3  
> Created at: 2017-12-28 14:11:34 UTC  
> Updated at: 2018-01-17 14:12:13 UTC  
> Closed at: 2018-01-17 14:12:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/63  

https://svn.boost.org/trac10/ticket/11142

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-12-31 05:24:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-354586059  

Looking at the documentation the period_formatter delimiter_strings function was never documented. If we are going to make changes so that this function actually works, should we not document the fact ?

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-12-31 15:19:05 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-354609308  

I would say it is poorly documented.  I will update the documentation.  Or we could just remove it, since nobody ever used it, and it's easy to construct a new one.

---

## Comment 3

> Username: mclow  
> Created_at: 2018-01-01 18:38:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-354668535  

> Or we could just remove it, since nobody ever used it,   
  
I have been surprised many times after saying "Nobody ever used XXX" and been proven wrong.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-01-01 22:02:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-354680420  

In this case the code was never hooked up to anything so in fact it was never used or nobody got any value from it.

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-01-07 15:41:55 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-355831266  

Nobody got any value from it because it had not been implemented. Also, while the function was mentioned, it was undocumented as to what it was supposed to do. If you want to have this PR pushed, then we need to document what 'delimiter_strings' actually now does with your fix, even if it is as simply as saying that the function offers a way of changing the delimiter strings for the period_formatter after the period_formatter has been constructed.

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-01-12 16:48:12 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357291525  

I added a comment to the method in the header.

---

## Comment 7

> Username: eldiener  
> Created_at: 2018-01-12 20:28:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357345504  

It needs actual documentation.

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-01-12 20:51:25 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357350529  

Will this be sufficient?  
```  
-    //! Change the delimiter strings  
+    //! Modify the delimiter strings used in formatting.  
+    //! \param[in]  separator  the new separator to use  
+    //! \param[in]  start_delim  the new start delimiter to use  
+    //! \param[in]  open_end_delim  the new open-ended delimiter to use  
+    //! \param[in]  closed_end_delim  the new closed-ended delimiter to use  
     void delimiter_strings(const string_type& separator,  
                            const string_type& start_delim,  
```

---

## Comment 9

> Username: eldiener  
> Created_at: 2018-01-12 21:25:45 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357358440  

In the current docs, in http://www.boost.org/doc/libs/1_66_0/doc/html/boost/date_time/period_formatter.html, I see:  
  
6. void delimiter_strings(const string_type &, const string_type &,   
                       const string_type &, const string_type &);  
  
but no explanation of even what this does follows. If your doxygen addition generates at the least an  explanation for delimiter_strings in the final documentation then it is fine AFAIAC.

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-01-12 21:30:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357359469  

Waiting for the current builds to complete on CI to see what the state of things is.

---

## Comment 11

> Username: jeking3  
> Created_at: 2018-01-12 21:30:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357359668  

Oh, and yes, the documentation you referenced is auto-generated so comments from the header will end up there.

---

## Comment 12

> Username: swatanabe  
> Created_at: 2018-01-12 21:32:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357360025  

AMDG  
  
On 01/12/2018 01:51 PM, James E. King, III wrote:  
> Will this be sufficient?  
> ```  
> -    //! Change the delimiter strings  
> +    //! Modify the delimiter strings used in formatting.  
> +    //! \param[in]  separator  the new separator to use  
> +    //! \param[in]  start_delim  the new start delimiter to use  
> +    //! \param[in]  open_end_delim  the new open-ended delimiter to use> +    //! \param[in]  closed_end_delim  the new closed-ended delimiter  
to use  
  
  
  I don't think that "open-ended" and "closed-ended" are the  
correct terms.  What the parameters mean are the delimiters  
for ending open and closed ranges, respectively.  What you  
wrote here sounds like blindly transliterating the parameter  
names into English words.  
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: jeking3  
> Created_at: 2018-01-12 22:57:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357377325  

Thanks, I'll fix that up before committing.  
  
On the build failures, I'm going to kick a build of "develop" without these changes and see where it stands; if the same three windows jobs fail for "develop" then we can merge this little change and focus on fixing up the CI builds.  Also wow, about 15 minutes per build job adds up quickly.

---

## Comment 14

> Username: jeking3  
> Created_at: 2018-01-12 23:04:41 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357378737  

These windows failures are in the same jobs at the other PR:  
  
https://ci.appveyor.com/project/jeking3/date-time-1evbf/build/1.0.1-develop  
  
Per http://www.boost.org/doc/libs/1_66_0/more/getting_started/windows.html:  
  
```  
A note to Cygwin and MinGW users  
  
If you plan to use your tools from the Windows command prompt, you're in the right place.   
If you plan to build from the Cygwin bash shell, you're actually running on a POSIX platform   
and should follow the instructions for getting started on Unix variants.  Other command shells,   
such as MinGW's MSYS, are not supported—they may or may not work.  
```  
  
Technically we meet that bar right now, but I will look into the others.

---

## Comment 15

> Username: eldiener  
> Created_at: 2018-01-12 23:25:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357382110  

MingW ( mingw-64) works from the Windows command prompt without using MSYS at all. We do support that. Cygwin is a different story AFAIK.

---

## Comment 16

> Username: eldiener  
> Created_at: 2018-01-13 01:32:20 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357399168  

I have tested your PR locally on Windows 8.1 64-bit with mingw-64/gcc-6.3-x64 in 64-bit mode and all tests pass either in 03 and 11 mode. So something must be wrong in the way AppVeyor is setup to run the test or in the AppVeyor environment. Since you were the one to get AppVeyor working for date_time maybe you can figure out why its results are faulty.

---

## Comment 17

> Username: jeking3  
> Created_at: 2018-01-13 14:06:36 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357438035  

The published documentation says we don't however.  I wonder if it should be changed once we get all of them building in CI for all the repositories.

---

## Comment 18

> Username: jeking3  
> Created_at: 2018-01-13 16:31:01 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357447809  

@eldiener I will take a look at those, yes.

---

## Comment 19

> Username: jeking3  
> Created_at: 2018-01-15 16:22:34 UTC  
> Updated_at: 2018-01-15 16:24:09 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357729265  

One issue is that the data file has different linefeed endings.  To prevent this issue from cropping up in the future, I'm adding a boost::trim_right() to the getline parser.  This would have been introduced with the updated timezone database (recently).  
  
Another issue is a y2038 issue on cygwin 32-bit where std::time_t is 32-bits.  I am working on that one.  Also see: https://github.com/Alexpux/Cygwin/commit/4de8754bac676fc7f29082ccf8281b1c73ce629e

---

## Comment 20

> Username: eldiener  
> Created_at: 2018-01-15 16:47:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357736084  

For a text file git should take care of the line endings. On Windows it is cr-lf while on Linux it is just lf. I am not sure on Apple what it is. But whatever it is git should handle it. There should never be a need for special line cropping code. Is not the timezone database a text file and is not the timezone database under git  ?

---

## Comment 21

> Username: swatanabe  
> Created_at: 2018-01-15 17:09:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357741326  

AMDG  
  
On 01/15/2018 09:47 AM, Edward Diener wrote:  
> For a text file git should take care of the line endings. On Windows it is cr-lf while on Linux it is just lf. I am not sure on Apple what it is.  
  
Apple uses lf (I believe ancient Apple used cr, but  
that no longer matters.)  
  
> But whatever it is git should handle it. There should never be a need for special line cropping code. Is not the timezone database a text file and is not the timezone database under git  ?  
>   
  
  Unfortunately, it's not that simple.  The release tarballs  
should at least not break if you use the *.zip on a unix  
system or *.tar.gz on windows.  In addition, having the wrong  
line endings is inevitable, if you're using a single checkout  
shared between Windows and Unix.  
  
In Christ,  
Steven Watanabe

---

## Comment 22

> Username: eldiener  
> Created_at: 2018-01-15 17:14:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357742660  

My point is that if it is a text file read line by line the code on whatever OS should take care of reading the data without worrying about the line endings, as long as the line endings are manipulated by git and git knows the file is a text file and not a binary file. Of course if the data in the file is being read in binary mode, where the data itself is a text mode file, then the code is wrong.

---

## Comment 23

> Username: swatanabe  
> Created_at: 2018-01-15 17:44:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357749176  

AMDG  
  
On 01/15/2018 10:14 AM, Edward Diener wrote:  
> My point is that if it is a text file read line by line the code on whatever OS should take care of reading the data without worrying about the line endings, as long as the line endings are manipulated by git and git knows the file is a text file and not a binary file. Of course if the data in the file is being read in binary mode, where the data itself is a text mode file, then the code is wrong.  
>   
  
I understand what you're saying, but it's not enough.  I already  
stated two cases where your model does not work and cannot work.  
(I, personally, use a single checkout shared between multiple  
virtual machines with different OSes.)  
  
In Christ,  
Steven Watanabe

---

## Comment 24

> Username: jeking3  
> Created_at: 2018-01-15 18:36:47 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357759567  

The fix was easy to strip any whitespace off the end of each line.  This takes care of many possible future issues with folks editing the file.

---

## Review 25 [Commented]

> Username: pdimov  
> Created_at: 2018-01-15 19:30:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/63#pullrequestreview-88915163  

📁 Jamfile

```diff
   7 |+ #  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   8 |+ 
   9 |+ project libs/date_time ;
```

> Username: pdimov  
> Created_at: 2018-01-15 19:30:46 UTC  
> Updated_at: 2018-01-16 21:33:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#discussion_r161599870  

Not sure that one should go out of his way to add project names when they aren't needed; seems to me that this brings no benefit and can only cause problems.

> Username: jeking3  
> Created_at: 2018-01-16 14:35:29 UTC  
> Updated_at: 2018-01-16 21:33:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#discussion_r161774118  

Okay, I can remove it - should it also be removed from libs/date_time/test/Jamfile as well?  Only the build/ one should have a project by name?  (Also curious why date_time has a library at all... it doesn't look like it really needs one for the scant few items in src/)  
  
Still working out the MinGW issues; got cygwin building clean and fixed a y2038 issue so far in my build investigation.


---

## Comment 26

> Username: swatanabe  
> Created_at: 2018-01-15 20:55:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-357787120  

AMDG  
  
  Right.  project names should only be used in Jamroot  
and when you explicitly intend to export the name for  
use in other projects.  (e.g. /boost//regex is the most  
idiomatic way to refer to a library.)  I have some ideas  
to make project names safer, but that's for the far  
future, if ever.  
  
In Christ,  
Steven Watanabe

---

## Comment 27

> Username: jeking3  
> Created_at: 2018-01-16 21:35:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-358114541  

@eldiener Hopefully the push I just made completes (the last one did - I just reordered the builds); I was able to get all build flavors to pass, and I can apply these cygwin and mingw changes back to Uuid and Format at some point and expand their test coverage; further having the CI build complete gives people an example of how to build with each toolchain.

---

## Comment 28

> Username: eldiener  
> Created_at: 2018-01-17 02:56:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-358179641  

It looks good. If the Travis CI tests pass I can merge the PR or you can do it yourself now.

---

## Comment 29

> Username: jeking3  
> Created_at: 2018-01-17 14:09:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-358315331  

I noticed that I added project statement to the doc and xmldoc build files as well so I will remove those when I merge this, when I will refresh the other pull requests out there.  We have to do something about these CI build delays though.  It shouldn't take 2 hours to run two gcc build jobs!

---

## Comment 30

> Username: jeking3  
> Created_at: 2018-01-17 14:12:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/63#issuecomment-358315990  

Closing, merging manually.

---
