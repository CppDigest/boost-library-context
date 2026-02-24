# #14 Master had an additional commit not in develop [Merged]

> Username: jeking3  
> Created at: 2017-10-07 13:47:20 UTC  
> Updated at: 2017-10-18 19:24:07 UTC  
> Merged at: 2017-10-07 13:59:08 UTC  
> Closed at: 2017-10-07 13:59:08 UTC  
> Url: https://github.com/boostorg/format/pull/14  

Changed 'less' to 'fewer' in an error message. Patch from @tempoz  
  
I found a commit in boost format master branch not in develop:  
```  
jking@ubuntu:~/boost/libs/format$ git checkout master  
Switched to branch 'master'  
Your branch is up-to-date with 'origin/master'.  
jking@ubuntu:~/boost/libs/format$ git blame include/boost/format/exceptions.hpp | grep '  7'  
0fde2d2b (Gennaro Prota 2006-07-20 13:36:16 +0000   7) // Distributed under the Boost Software License, Version 1.0.  
18785faa (Samuel Krempp 2004-01-24 17:33:18 +0000  70)             too_many_args(std::size_t cur, std::size_t expected)   
18785faa (Samuel Krempp 2004-01-24 17:33:18 +0000  71)                 : cur_(cur), expected_(expected) {}  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  72)             std::size_t get_cur() const { return cur_; }  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  73)             std::size_t get_expected() const { return expected_; }  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  74)             virtual const char *what() const throw() {  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  75)                 return "boost::too_many_args: "  
d8053752 (Marshall Clow 2015-05-18 08:34:53 -0700  76)                     "format-string referred to fewer arguments than were passed";  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  77)             }  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  78)         };  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  79)   
  
jking@ubuntu:~/boost/libs/format$ git checkout develop  
Switched to branch 'develop'  
Your branch is up-to-date with 'origin/develop'.  
jking@ubuntu:~/boost/libs/format$ git blame include/boost/format/exceptions.hpp | grep '  7'  
fe2c3f37 (Gennaro Prota 2006-07-20 12:13:16 +0000   7) // Distributed under the Boost Software License, Version 1.0.  
18785faa (Samuel Krempp 2004-01-24 17:33:18 +0000  70)             too_many_args(std::size_t cur, std::size_t expected)   
18785faa (Samuel Krempp 2004-01-24 17:33:18 +0000  71)                 : cur_(cur), expected_(expected) {}  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  72)             std::size_t get_cur() const { return cur_; }  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  73)             std::size_t get_expected() const { return expected_; }  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  74)             virtual const char *what() const throw() {  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  75)                 return "boost::too_many_args: "  
fe2c3f37 (Gennaro Prota 2006-07-20 12:13:16 +0000  76)                     "format-string referred to less arguments than were passed";  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  77)             }  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  78)         };  
beb10ca1 (Samuel Krempp 2004-01-21 11:51:23 +0000  79)   
  
jking@ubuntu:~/boost/libs/format$ git show d8053752  
commit d80537520b8aeaa51271acfd2eeb3c6a522fb04e  
Author: Marshall Clow <marshall@idio.com>  
Date:   Mon May 18 08:34:53 2015 -0700  
  
    Changed 'less' to 'fewer' in an error message. Patch from @tempoz  
  
diff --git a/include/boost/format/exceptions.hpp b/include/boost/format/exceptions.hpp  
index 9b2de83..56ee30d 100644  
--- a/include/boost/format/exceptions.hpp  
+++ b/include/boost/format/exceptions.hpp  
@@ -73,7 +73,7 @@ namespace boost {  
             std::size_t get_expected() const { return expected_; }  
             virtual const char *what() const throw() {  
                 return "boost::too_many_args: "  
-                    "format-string referred to less arguments than were passed";  
+                    "format-string referred to fewer arguments than were passed";  
             }  
         };  
```

---

## Comment 1

> Username: mclow  
> Created_at: 2017-10-07 14:39:46 UTC  
> Url: https://github.com/boostorg/format/pull/14#issuecomment-334939609  

I remember screwing this up; I thought that I fixed it in `develop` as well. Apparently not. Thanks.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-07 15:26:55 UTC  
> Url: https://github.com/boostorg/format/pull/14#issuecomment-334943210  

@eldiener can you merge develop into master so this and the ubsan fix are in the next release?  Should items in boost trac be marked as resolved when they are fixed in develop, and then a milestone added when the fix is merged into master?  What's the protocol there?  Thanks.

---

## Comment 3

> Username: mclow  
> Created_at: 2017-10-07 15:27:56 UTC  
> Url: https://github.com/boostorg/format/pull/14#issuecomment-334943282  

I usually note in the Trac ticket the commit that fixed it in develop, and close the ticket when things get merged to master.

---

## Comment 4

> Username: eldiener  
> Created_at: 2017-10-07 15:28:58 UTC  
> Url: https://github.com/boostorg/format/pull/14#issuecomment-334943352  

I will merge into master when the regression tests cycle. That is usually in about a week.

---
