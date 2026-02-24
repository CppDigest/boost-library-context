# #9 Add copyright notice and license text to test program [Closed]

> Username: mloskot  
> Created at: 2019-08-08 19:46:08 UTC  
> Updated at: 2022-03-12 08:43:06 UTC  
> Closed at: 2022-03-12 08:43:06 UTC  
> Url: https://github.com/boostorg/array/pull/9  

Please the inspect tool which reported the following problems:  
  
### Before  
  
```  
bionic:/mnt/d/boost.wsl/libs/array$ inspect -text  
Boost Inspection Report  
Run Date: 19:16:37 UTC, Thursday 08 August 2019  
  
Totals:  
  22 files scanned  
  7 directories scanned (including root)  
  3 problems reported  
  
  
Problem counts:  
  1 files missing Boost license info or having wrong reference text  
  1 files missing copyright notice  
  0 files with invalid line endings  
  1 files that don't end with a newline  
  0 bookmarks with invalid characters  
  0 duplicate bookmarks  
  0 invalid urls  
  0 broken links  
  0 unlinked files  
  0 file and directory name issues  
  0 files with tabs  
  0 files with non-ASCII chars  
  0 files with Apple macros  
  0 files with a C-style assert macro  
  0 files with a deprecated BOOST macro  
  0 violations of the Boost min/max guidelines  
  0 usages of unnamed namespaces in headers (including .ipp files)  
  
Worst Offenders:  
test 3  
  
Summary:  
  test (3)  
  
Details:  
  *Lic* missing Boost license info, or wrong reference text  
  *C* missing copyright notice  
  *EOL* invalid (cr only) line-ending  
  *END* file doesn't end with a newline  
  *LINK* invalid bookmarks, duplicate bookmarks, invalid urls, broken links, unlinked files  
  *N* file and directory name issues  
  *Tabs* tabs in file  
  *ASCII* non-ASCII chars in file  
  *APPLE-MACROS* calls to Apple's debugging macros in file  
  *ASSERT-MACROS* presence of C-style assert macro in file (use BOOST_ASSERT instead)  
  *DEPRECATED-MACROS* presence of deprecated BOOST macro in file (see docs for replacements)  
  *M* uses of min or max that have not been protected from the min/max macros, or unallowed #undef-s  
  *U* unnamed namespace in header  
  
Directories with a file named "boost-no-inspect" will not be inspected.  
Files containing "boost-no-inspect" will not be inspected.  
  
|test|  
  test/test_cmake/main.cpp:  
    *C*  
    *END* file doesn't end with a newline  
    *Lic*  
```  
  
### After  
  
```  
bionic:/mnt/d/boost.wsl/libs/array$ inspect -text  
Boost Inspection Report  
Run Date: 19:39:52 UTC, Thursday 08 August 2019  
  
Totals:  
  22 files scanned  
  7 directories scanned (including root)  
  0 problems reported  
  
  
Problem counts:  
  0 files missing Boost license info or having wrong reference text  
  0 files missing copyright notice  
  0 files with invalid line endings  
  0 files that don't end with a newline  
  0 bookmarks with invalid characters  
  0 duplicate bookmarks  
  0 invalid urls  
  0 broken links  
  0 unlinked files  
  0 file and directory name issues  
  0 files with tabs  
  0 files with non-ASCII chars  
  0 files with Apple macros  
  0 files with a C-style assert macro  
  0 files with a deprecated BOOST macro  
  0 violations of the Boost min/max guidelines  
  0 usages of unnamed namespaces in headers (including .ipp files)  
```

---

## Comment 1

> Username: mclow  
> Created_at: 2019-08-08 19:52:01 UTC  
> Url: https://github.com/boostorg/array/pull/9#issuecomment-519663402  

Ok; apparently "Mike Dev" is an actual contributor.  My bad.  
However, I'll still stand by my comment. I'm not putting a copyright statement into this library for a pseudonym.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-08-08 19:57:24 UTC  
> Url: https://github.com/boostorg/array/pull/9#issuecomment-519665151  

I understand, but I just copied the existing notice from  
  
https://github.com/boostorg/array/blob/e5f4af3dd212608f6a4a639a82f3484234807eec/test/test_cmake/CMakeLists.txt#L1-L2  
  
FYI, on Boost channel at Slack, here https://cpplang.slack.com/archives/C27KZLB0X/p1565292169300800, I raised the issue of credibility of such notice.  
  
I'm acting here as just a janitor, so I'll leave the debate between you and @Mike-Devel

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2019-08-09 07:46:22 UTC  
> Url: https://github.com/boostorg/array/pull/9#issuecomment-519817629  

I'm completely fine with not being mentioned in the copy right notice for a trivial piece of code like this. When I made one of my first cmake PRs, someone told me to add it and I copied it over to most cmake PRs I made since then, but apparently forgot a file here and there.   
  
I'm sorry if my wish to stay anonymous causes trouble for you. As this PR more than doubles the number of lines, how about you @mloskot put your own name into the copyright?

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-08-09 07:52:39 UTC  
> Updated_at: 2019-08-09 07:54:07 UTC  
> Url: https://github.com/boostorg/array/pull/9#issuecomment-519819617  

@Mike-Devel The trait of triviality or not, the fact it doubles LOCs or not is orthogonal and irrelevant to the rationale behind this PR. AFAIU, every single character in Boost source files is owned and must come with "clear copyright information", and that is a formal requirement, and that is what the `inspect` verifies.  
  
Perhaps those files should be just removed.   
I will leave it to @mclow, the product owner here, to decide.   
I just noticed the problem and made the tidying-up contribution. I don't quite care myself what happens with it next.

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-08-09 08:13:53 UTC  
> Url: https://github.com/boostorg/array/pull/9#issuecomment-519826246  

> Perhaps those files should be just removed.  
  
@mclow: I can make a PR that removes the whole test_cmake folder (and the accompanying test rule from CI), but I'd really prefer, if the CMakeLists.txt file int the root folder (Which also has my pseudonym in it) got not removed.  
  
It would be really said if - after all the work - it suddenly turned out that anonymous contributions to boost where not allowed.  
  
> AFAIU, every single character in Boost source files is owned and must come with "clear copyright information"  
  
I think I read somewhere on the boost homepage that it is up to the individual contributor to decide if his contribution is substantial enough to warrant a mention in the copyright notices. Generally speaking, having copyright on something requires a certain level of originality https://en.wikipedia.org/wiki/Threshold_of_originality (Despite what some file headers claim, you can't have copy-right on the source code for a hello world program) and I'm pretty sure none of things I contributed would qualify.  
  
If I only changed a few lines in an existing file, I don't think anyone would have cared  about me adding my name, but in this case it just so happened that I was the one who added the file.   
  
If that really becomes a point of contention: Can't I just make a blanked statement somewhere that I transfer any copyright I might hold over contributions made under this pseudonym to the boost organization or a particular member their of?

---

## Comment 6

> Username: pabristow  
> Created_at: 2019-08-19 09:49:36 UTC  
> Url: https://github.com/boostorg/array/pull/9#issuecomment-522496875  

I agree that Boost should not have any copyright claims by Anon (or 'John Doe'); this is the established practice.  
  
But are making a mountain out of a molehill?  We have a Community maintenance team.  One of them should take over the code and make the copyright claim (if the original author doesn't want to).

---
