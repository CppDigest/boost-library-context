# #425 - Extract individual library versions [Closed]

> Username: Mike-Devel  
> Created at: 2018-12-17 15:01:27 UTC  
> Updated at: 2025-05-10 01:51:04 UTC  
> Closed at: 2025-05-10 01:49:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/425  

Is there a simple way to only extract the files relevant to Spirit classic/v2/x3? Unfortunately, it is not at all clear from the project structure what belongs to what.   
  
  
Have you considered to use a separate repository for each of those libraries (not sure if boost rules allow this, or be desirable for your project at all - just a thought that came to my mind)?

---

## Comment 1

> Username: teajay-fr  
> Created at: 2018-12-17 20:12:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/425#issuecomment-447982524  

If I recall correctly there is a tool to extract boost libraries with all their dependencies. You can take a look at this stackoverflow question:  
https://stackoverflow.com/questions/2150836/how-to-extract-the-boost-interprocess-library (https://stackoverflow.com/questions/2150836/how-to-extract-the-boost-interprocess-library)  
Hope this helps.  
  
Thomas Bernard  
  
17 décembre 2018 16:05 "Mike-Devel" <notifications@github.com (mailto:%22Mike-Devel%22%20<notifications@github.com>)> a écrit:  
	Is there a simple way to only extract the files relevant to Spirit classic/v2/x3? Unfortunately, it is not at all clear from the project structure what belongs to what.   
  
	Have you considered to use a separate repository for each of those libraries (not sure if boost rules allow this)?   
  
	—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub (https://github.com/boostorg/spirit/issues/425), or mute the thread (https://github.com/notifications/unsubscribe-auth/AFt5U5EEhlC3ZpNQ9rrgeZ9y-ifetOEdks5u57HJgaJpZM4ZWhjf).

---

## Comment 2

> Username: Mike-Devel  
> Created at: 2018-12-17 21:04:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/425#issuecomment-447998015  

I probably should have been more explicit. I meant only the files for classic **OR** for v2 **OR** x3 from this repository. IIRC, bcp will not distinguish between them.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-12-18 13:50:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/425#issuecomment-448227144  

The Spirit has a very clear structure. Splitting is not a thing that is promoted or supported, but should be simple to achieve.  
  
  - Spirit X3 `boost/spirit/home/x3.*`  
  - Spirit V2 `boost/spirit/home/support.*`  
    - Qi `boost/spirit/home/qi.*`  
      - Shortcuts `boost/spirit/include/qi.*`  
      - Repository `boost/spirit/repository/home/qi.*`  
        - Shortcuts `boost/spirit/repository/include/qi.*`  
    - Karma `boost/spirit/home/karma.*`  
      - Shortcuts `boost/spirit/include/karma.*`  
      - Repository `boost/spirit/repository/home/karma.*`  
        - Shortcuts `boost/spirit/repository/include/karma.*`  
    - Lex `boost/spirit/home/lex.*`  
      - Shortcuts `boost/spirit/include/lex.*`  
  - Classic `boost/spirit/home/classic.*`  
  
You also can list the only headers that is required with Clang `echo #include "boost/spirit/home/x3.hpp" | clang -I"path/to/boost" -H -fsyntax-only -x c++ - 2>&1 | grep -oe boost/spirit/.*`

---

## Comment 4

> Username: Mike-Devel  
> Created at: 2018-12-18 15:33:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/425#issuecomment-448261315  

x3 seems to also have some dependencies on `boost/spirit/home/support/` e.g. here: https://github.com/boostorg/spirit/blob/e14eaa3057a48776371a557631422deeb0ad81de/include/boost/spirit/home/x3/char/char.hpp#L11-L14.  
  
  
Thank you very much for the script that will be very helpful. Seems like GitHub has eaten some of your quotes though. Should be:   
  
  
    echo '#include "boost/spirit/home/x3.hpp"' | clang -I"path/to/boost" -H -fsyntax-only -x c++ - 2>&1 | grep -oe "boost/spirit/.*"

---

## Comment 5

> Username: Kojoley  
> Created at: 2020-01-16 14:27:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/425#issuecomment-575175533  

@djowel how do you look at splitting classic into its own repository? There are some precedents in boost of doing that to break dependency cycles. It will also decrease the number of dependencies people have to install if they are not needed classic (boost_thread and its subdependencies).

---

## Comment 6

> Username: djowel  
> Created at: 2020-01-24 08:57:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/425#issuecomment-578045485  

> @djowel how do you look at splitting classic into its own repository? There are some precedents in boost of doing that to break dependency cycles. It will also decrease the number of dependencies people have to install if they are not needed classic (boost_thread and its subdependencies).  
  
Good question. I think it is a good idea.

---

## Comment 7

> Username: Mike-Devel  
> Created at: 2020-01-24 16:34:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/425#issuecomment-578204135  

Would it make sense to go even one step further and separate each version (classic, v2, x3) into a separate repo? Or are v2 and x3 closely coupled?

---

## Comment 8

> Username: saki7  
> Created at: 2025-05-10 01:49:24 UTC  
> Updated at: 2025-05-10 01:50:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/425#issuecomment-2868213643  

We now have modular boost system that allows you to use individual libraries separately, and there's an ongoing discussion on splitting Classic.  
  
Closing this as this issue is too broad; we must discuss on more specific level.
