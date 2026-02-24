# #19 Ported filesystem files for Windows Phone 8.1 [Closed]

> Username: mosherubin  
> Created at: 2015-11-03 18:01:13 UTC  
> Updated at: 2020-05-01 20:58:46 UTC  
> Closed at: 2020-05-01 20:58:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19  

This PR presents four files in the 'filesystem' project that needed to be ported for Windows Phone 8.1.

---

## Comment 1

> Username: Beman  
> Created_at: 2015-11-04 14:25:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19#issuecomment-153739711  

This pull request is mostly the removal of extra spaces at line endings. Is that intended? Do the extra spaces have some effect on Windows Phone 8.1?  
  
Thanks,  
  
--Beman

---

## Comment 2

> Username: mosherubin  
> Created_at: 2015-11-04 15:14:06 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19#issuecomment-153757788  

Hi!  
  
After I submitted the pull request I noticed many of the extra spaces on lines in include/boost/filesystem/path.hpp.  I do not know why this happened, and those lines can be ignored if so desired.  Having said that, maybe the extra spaces at the end should be cleaned up.  There are, however, two lines I added to this header file: line 455 and 470.  I added these because, when compiling for WinRT, the function name 'generic' clashed with some other symbol, which I was unable to locate.  I therefore #ifdef out the experimental generic() function for WinRT.  
  
The lion's share of new code is found in operations.cpp. and can be found in "#if BOOST_PLAT_WINDOWS_RUNTIME" blocks ("+318 - 125").  
  
The other two files have small but important changes in them.  
  
Thanks,  
  
Moshe

---

## Comment 3

> Username: mosherubin  
> Created_at: 2015-11-04 18:54:35 UTC  
> Updated_at: 2015-11-05 04:03:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19#issuecomment-153829301  

@beman I did a binary diff and, indeed, there are extra spaces at the end of many lines.  For example "#else<space><cr><lf>" in the original, "#else<cr><lf>" in the pushed one.  The extra spaces are harmless to WinRT, but maybe now is the right time to remove the extra spaces.  
  
Moshe

---

## Comment 4

> Username: mosherubin  
> Created_at: 2015-11-05 08:41:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19#issuecomment-153990182  

Hi Beman,  
  
Doing a binary search through previous revisions of include/boost/filesystem/path.hpp shows the following:  
- The path.hpp file with extra spaces at the end of lines appeared on March 26, 2012 [(see this commit)](https://github.com/boostorg/filesystem/commit/16099b4c7dec36f22d7075784f049286ee07bc76#diff-7ba71d400a12ad094099e04cde0ade9e).  In this commit, path.hpp was copied from the now-defunct `v3` folder.  Once the copy was done, there were extra spaces on some lines (e.g., line 65 "# else").  
- Previous versions of path.hpp, although with different content, did not have extra spaces.  
  
If you agree, it might be a good move now to remove the extra spaces.  Of course, this is your call. :-).  
  
Thanks,  
  
Moshe

---

## Comment 5

> Username: KindDragon  
> Created_at: 2015-12-10 16:16:33 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19#issuecomment-163674495  

Maybe better remove extra spaces in different commit from changes?

---

## Comment 6

> Username: Lastique  
> Created_at: 2019-04-16 09:17:49 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19#issuecomment-483580081  

If this is still relevant, please rebase against current develop.

---

## Comment 7

> Username: Lastique  
> Created_at: 2019-04-16 09:18:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19#issuecomment-483580375  

Also, see https://github.com/boostorg/filesystem/pull/18.

---

## Comment 8

> Username: Lastique  
> Created_at: 2020-05-01 20:58:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/19#issuecomment-622565785  

Closing as the PR is inactive.

---
