# #22 Ported regex files for Windows Phone 8.1 [Open]

> Username: mosherubin  
> Created at: 2015-11-03 12:12:27 UTC  
> Updated at: 2018-11-11 14:53:08 UTC  
> Url: https://github.com/boostorg/regex/pull/22  

Three (3) source files and one (1) header file modified to port regex for Windows Phone 8.1.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-11-03 17:17:41 UTC  
> Updated_at: 2015-11-04 10:58:51 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43777358  

Given the assert earlier, these checks look redundant to me?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-11-03 17:18:04 UTC  
> Updated_at: 2015-11-04 10:58:51 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43777430  

We can't use internal asio macros here.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-11-03 17:22:59 UTC  
> Url: https://github.com/boostorg/regex/pull/22#issuecomment-153424084  

Thanks for the patches, I've made a couple of comments, can you tell me if it's possible to run the regression tests under windows phone emulation?  Many thanks!

---

## Comment 4

> Username: mosherubin  
> Created_at: 2015-11-03 20:35:56 UTC  
> Updated_at: 2015-11-04 10:58:51 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43803170  

The three functions:  
1. mapfile_iterator(const mapfile\* f, long arg_position)  
2. mapfile_iterator(const mapfile_iterator& i)  
3. ~mapfile_iterator()  
  
are not consistent in their manner of validity checking.  If BOOST_ASSERT is like its C counterpart, then it will only catch a null 'f' value in debug, not release.  For release mode, all validity checks must be in the form of C++ comparison statements.  For that matter, the BOOST_ASSERT() macro should be used in all three functions.  
  
In addition, the check of "if (file && node)" was being done in only function no. 1, not in 2 and 3.  IMHO, the check should be done in all three functions (and not in 1 & 2 only as I submitted).  
  
Both "file" and "node' should be checked for null values in all three functions.

---

## Comment 5

> Username: mosherubin  
> Created_at: 2015-11-03 20:42:34 UTC  
> Updated_at: 2015-11-04 10:58:51 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43803874  

Good catch.  Indeed, the line should read:  
  
#if defined (BOOST_**PLAT**_WINDOWS_RUNTIME) || defined (BOOST_PLAT_WINDOWS_PHONE)  
  
(Note the use of 'PLAT' rather than 'ASIO'.) This must have leaked through from my port of ASIO and submitted to Chris Kohlhoff. :-)  
  
I'd like to make the change to the #ifdef statement.  I'm relatively new to Git: how can I make that change?  Do I need to submit a new pull request?

---

## Comment 6

> Username: mosherubin  
> Created_at: 2015-11-03 20:56:35 UTC  
> Url: https://github.com/boostorg/regex/pull/22#issuecomment-153485687  

@jzmaddock Thanks for the kind words! From responses I received from the Boost mailing list, I am not aware of being able to run the Boost regression tests for Windows Phone. I will say that I have been using my fixes, originally done on version 1.48, for the past few months with no problems at all, but that will not convince you or anyone else they are good :-).

---

## Comment 7

> Username: MarcelRaad  
> Created_at: 2015-11-03 21:35:25 UTC  
> Updated_at: 2015-11-04 10:58:51 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43810458  

When building for WinRT, BOOST_NO_ANSI_APIS is always defined (see https://github.com/boostorg/config/pull/80), so this won't work. It also makes some of the added checks redundant.

---

## Comment 8

> Username: mosherubin  
> Created_at: 2015-11-04 11:01:07 UTC  
> Updated_at: 2015-11-04 11:01:55 UTC  
> Url: https://github.com/boostorg/regex/pull/22#issuecomment-153684928  

@MarcelRaad I have removed all references to WinRT preprocessor symbols.  This because line 22 shows:  
  
`#if defined(_WIN32)` && !defined(BOOST_REGEX_NO_W32) && !defined(BOOST_REGEX_NO_WIN32_LOCALE)  
  
The `defined(_WIN32)` means that, when compiling for WinRT, the entire #if block is ignored.

---

## Comment 9

> Username: mosherubin  
> Created_at: 2015-11-04 11:06:16 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43867145  

Please see comment #19 for a similar discussion.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2015-11-04 17:45:24 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43913209  

On 03/11/2015 20:35, mosherubin wrote:  
  
> In include/boost/regex/v4/fileiter.hpp   
> https://github.com/boostorg/regex/pull/22#discussion_r43803170:  
>   
> > @@ -229,14 +229,15 @@ class BOOST_REGEX_DECL mapfile_iterator  
> >        file = f;  
> >        node = f->_first + arg_position / mapfile::buf_size;  
> >        offset = arg_position % mapfile::buf_size;  
> > -      file->lock(node);  
> > -      if(file && node)  
>   
> The three functions:  
> 1. mapfile_iterator(const mapfile\* f, long arg_position)  
> 2. mapfile_iterator(const mapfile_iterator& i)  
> 3. ~mapfile_iterator()  
>   
> are not consistent in their manner of validity checking. If   
> BOOST_ASSERT is like its C counterpart, then it will only catch a null   
> 'f' value in debug, not release. For release mode, all validity checks   
> must be in the form of C++ comparison statements. For that matter, the   
> BOOST_ASSERT() macro should be used in all three functions.  
>   
> In addition, the check of "if (file && node)" was being done in only   
> function no. 1, not in 2 and 3. IMHO, the check should be done in all   
> three functions (and not in 1 & 2 only as I submitted).  
>   
> Both "file" and "node' should be checked for null values in all three   
> functions.  
  
You need to realise that these are strictly internal implementation   
details that are only constructed in certain ways (they're also   
deprecated so we're arguing over angels on a pinhead, but never mind).  
  
The following invariants hold:  
  
if file is NULL then node is NULL.  
If file is not NULL then node is not NULL.  
When constructing from a mapfile then the argument is never NULL.  
  
IMO using asserts to check these invariants is exactly the correct thing   
to do.  
  
Best, John.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2015-11-04 17:46:19 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43913327  

> I'd like to make the change to the #ifdef statement. I'm relatively   
> new to Git: how can I make that change? Do I need to submit a new pull   
> request?  
  
No just push the change to the cloned repro you used for the PR and the   
PR will get updated automatically.  
  
Thanks, John.

---

## Comment 12

> Username: mosherubin  
> Created_at: 2015-11-04 18:03:06 UTC  
> Url: https://github.com/boostorg/regex/pull/22#discussion_r43915357  

Hi John,  
  
I most certainly defer to you on these issues :+1: !  Do you need me to make any changes or can you make them?  if the former, what would you like me to do?  
  
Regards,  
  
Moshe

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2015-11-07 09:18:40 UTC  
> Url: https://github.com/boostorg/regex/pull/22#issuecomment-154667668  

Moshe, as a temporary measure I've extended an existing fix for Windows store to cover all Windows runtime apps, including the phone: https://github.com/boostorg/regex/commit/eb729f6557f04475200fd78feb2cb43c9d6bbbe3  
  
This means disabling all Win32 API usage on these platforms which has the advantage that it's all well tested code (basically it's exacty the same configuration as used on all non-windows platforms).  
  
However, I'm leaving this PR open, and if I can ever figure out how to run our tests for on the Windows runtime, then I'll revisit this.  In the mean time I'll try to get this temporary fix into the next release.  
  
Best, John.

---

## Review 14 [Changes requested]

> Username: jeking3  
> Created_at: 2018-11-11 14:53:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/regex/pull/22#pullrequestreview-173701188  

Boost.Predef and Boost.WinApi gained more comprehensive UWP support in 2017 - recommend revisiting the definitions.  If definitions are missing from Boost.WinApi for things (like CreateFileMappingFromAppW) they could be added there instead.

---
