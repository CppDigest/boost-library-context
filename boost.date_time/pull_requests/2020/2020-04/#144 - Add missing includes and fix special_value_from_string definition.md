# #144 Add missing includes and fix special_value_from_string definition [Merged]

> Username: Lastique  
> Created at: 2020-04-04 14:31:00 UTC  
> Updated at: 2020-04-06 11:30:59 UTC  
> Merged at: 2020-04-05 14:27:28 UTC  
> Closed at: 2020-04-05 14:27:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/144  

This PR does the following:  
  
- Adds a test generator which checks if every public header is self-contained (i.e. compiles if included alone). This detects missing includes and general syntax and C++ errors. These tests are enabled on a few CI jobs.  
- Based on these test failures, fixes errors in headers, like missing includes and incorrect namespace qualification.  
- Moves `special_value_from_string` definition from `gregorian/greg_month.hpp` to `gregorian/parsers.hpp` and removes the obsolete declaration in `gregorian/parsers.hpp`. This fixes https://github.com/boostorg/date_time/issues/143.  
- Changes `from_string` and similar functions in `gregorian/parsers.hpp` to accept `std::string` by const reference instead of by value to avoid unnecessary copying.  
- Converts all tabs to spaces (as per our [coding guidelines](https://www.boost.org/development/requirements.html#Tabs)) and trims trailing spaces in a few places. (Sorry, couldn't resist on this part.)

---

## Review 1 [Commented]

> Username: JeffGarland  
> Created_at: 2020-04-04 14:55:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/144#pullrequestreview-387704824  

📁 include/boost/date_time/gregorian/parsers.hpp

```diff
  45 |+   }
  46 | 
  47 |   //! Deprecated: Use from_simple_string
```

> Username: JeffGarland  
> Created_at: 2020-04-04 14:42:48 UTC  
> Updated_at: 2020-04-05 08:51:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#discussion_r403477783  

funny -- this comment is super old -- from_simple_string should probably go too -- but for another time.


📁 .travis.yml

```diff
 126 |-     - { os: "linux", env: [ "B2_TOOLSET=gcc-8",       "B2_CXXSTD=17,2a"    ], addons: *gcc-8     }
 127 |-     - { os: "linux", env: [ "B2_TOOLSET=gcc-9",       "B2_CXXSTD=17,2a"    ], addons: *gcc-9     }
 122 |+                      env: [ "B2_TOOLSET=gcc-4.9",     "B2_CXXSTD=03,11",    "BOOST_DATE_TIME_TEST_WITHOUT_SELF_CONTAINED_HEADER_TESTS=1" ], addons: *gcc-49    }
```

> Username: JeffGarland  
> Created_at: 2020-04-04 14:50:55 UTC  
> Updated_at: 2020-04-05 08:51:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#discussion_r403478629  

so why turn off this test for these?

> Username: Lastique  
> Created_at: 2020-04-04 15:30:59 UTC  
> Updated_at: 2020-04-05 08:51:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#discussion_r403482724  

I only left the self-contained header tests enabled for one config on Linux and one on Windows/MSVC to conserve CI time. Note that on Linux, the tests run twice - for C++03 and C++11. Later C++ versions are unlikely to change standard headers as much as between C++03 and C++11.

> Username: JeffGarland  
> Created_at: 2020-04-05 00:18:45 UTC  
> Updated_at: 2020-04-05 08:51:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#discussion_r403592717  

that makes sense

> Username: Lastique  
> Created_at: 2020-04-05 09:03:17 UTC  
> Updated_at: 2020-04-05 09:03:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#discussion_r403671645  

Oh, correction - on Linux the tests are enabled on two jobs, not one - on gcc with libstdc++ and clang with libc++.

> Username: pdimov  
> Created_at: 2020-04-05 14:17:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#discussion_r403708068  

Unless the header tests take too much time, and they shouldn't, it might be worth it to just leave them enabled on all configurations. They catch some obvious mistakes (not just missing includes) without much cost.

> Username: JeffGarland  
> Created_at: 2020-04-05 14:23:45 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#discussion_r403708855  

idk, the CI takes too long already given that typically I've often had to wait hours for results.  Of course most of that is that it doesn't necessarily run right away.  And now we have had one run where a permitted failure wasn't classified right and stop the merge -- and a 'random failure' (no actual unexpected errors in log and passed the last time) stopping the merge...


📁 include/boost/date_time/date_parsing.hpp

```diff
  10 |  */
  11 | 
  12 |+ #include <map>
```

> Username: JeffGarland  
> Created_at: 2020-04-04 14:53:32 UTC  
> Updated_at: 2020-04-05 08:51:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#discussion_r403478902  

right nevermind on the other question -- circling back I see why you moved the includes not.  Of course the correct order for truly self contained headers is the std lib headers need to go after the boost headers.  But this is fine.


---

## Comment 2

> Username: JeffGarland  
> Created_at: 2020-04-04 14:56:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609040904  

Some CI failures  
  
.\boost/date_time/special_values_parser.hpp(135): note: see reference to function template instantiation 'short boost::date_time::string_parse_tree<charT>::match(std::istreambuf_iterator<char,std::char_traits<char>> &,std::istreambuf_iterator<char,std::char_traits<char>> &,boost::date_time::parse_match_result<charT> &,unsigned int &) const' being compiled  
        with  
        [  
            charT=char  
        ]  
.\boost/date_time/special_values_parser.hpp(41): note: see reference to class template instantiation 'boost::date_time::string_parse_tree<charT>' being compiled

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-04-04 15:38:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609046681  

> Some CI failures  
  
Should be fixed now.

---

## Comment 4

> Username: JeffGarland  
> Created_at: 2020-04-05 00:30:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609109384  

For reasons I can't understand the ubsan failure is not allowed now -- even though the issues are upstream of date-time   As a result github doesn't allow me to merge.  Maybe @jeking3 can chime in on that -- my read is that the .travis.yml should allow that failure.  Believe I can force it from the command line and will do tomorrow if no change is forthcoming.

---

## Comment 5

> Username: Lastique  
> Created_at: 2020-04-05 08:53:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609381903  

See if you can merge again.  
  
I'm not very familiar with Travis config, it seems one has to fully duplicate the job description in `allow_failures` section for it to match.

---

## Comment 6

> Username: Lastique  
> Created_at: 2020-04-05 09:06:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609383388  

Also, I think you can merge it even with failed or incomplete CI. It's just the merge button is not green, but it is still clickable.

---

## Comment 7

> Username: JeffGarland  
> Created_at: 2020-04-05 13:59:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609421061  

And with your force push the ubsan was correctly put in the allowable failures.  Unfortunately another failure showed up -- which doesn't seem to be an actual failure when I look thru the logs -- plus it was ok the first time.    
  
And no, the button is really not enabled -- I have to pull it, merge it, and push it outside the gui.  Doing that now...

---

## Comment 8

> Username: JeffGarland  
> Created_at: 2020-04-05 14:16:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609423328  

No joy -- even the command line is somehow able to check that merge. Can you just temporarily comment out the one that failed on the last run or put in the allowable group so we can get this merged?

---

## Comment 9

> Username: pdimov  
> Created_at: 2020-04-05 14:21:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609424057  

There's a branch protection rule on develop that prevents merging if Travis doesn't pass.  
  
@JeffGarland  I made you an admin; you can remove it.

---

## Comment 10

> Username: Lastique  
> Created_at: 2020-04-05 14:22:02 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609424083  

Can you go into the project settings? In there, select Branches, develop, Edit and untick "Require status checks to pass before merging".

---

## Comment 11

> Username: JeffGarland  
> Created_at: 2020-04-05 14:28:10 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609424861  

@pdimov thanks - that did the trick.

---

## Comment 12

> Username: Flamefire  
> Created_at: 2020-04-06 11:21:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609734236  

FTR: The failing "check that previous microsec_clock timestamp is less than the current" is non-critical?

---

## Comment 13

> Username: JeffGarland  
> Created_at: 2020-04-06 11:30:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/144#issuecomment-609738256  

Yes it is.  It's a long standing issue, that's says more about the platform than the library.  There have been changes in the last year that make it less flaky, but it's not 100%  
  
https://github.com/boostorg/date_time/issues/75

---
