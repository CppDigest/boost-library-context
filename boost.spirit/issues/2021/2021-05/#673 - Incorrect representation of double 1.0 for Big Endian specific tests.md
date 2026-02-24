# #673 - Incorrect representation of double 1.0 for Big Endian specific tests [Closed]

> Username: calvincramer  
> Created at: 2021-05-04 04:54:02 UTC  
> Updated at: 2021-05-06 12:09:17 UTC  
> Closed at: 2021-05-06 12:09:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/673  

The representation of 1.0 for 64-bit double in IEEE 754 should be 0x3ff0 0000 0000 0000, not 0x3f80 0000 0000 0000 in the following test cases:  
  
https://github.com/boostorg/spirit/blob/e30073e5bebb8102c73d1ad3588bf3f68cec2b64/test/qi/binary.cpp#L100  
https://github.com/boostorg/spirit/blob/e30073e5bebb8102c73d1ad3588bf3f68cec2b64/test/x3/binary.cpp#L140  
  
Looks like a simple copy-paste error, since 0x3f800... is 1.0 for single precision floats.  
Also it may be hard to test this, I don't know if boost tests at https://www.boost.org/development/tests/master/developer/spirit.html includes big-endian targets. If necessary I can test on PowerPC hardware.

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-05-04 17:24:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/673#issuecomment-832111541  

Indeed. I would appreciate if you run the tests on big-endian system and file a PR with necessary changes to make it pass.

---

## Comment 2

> Username: calvincramer  
> Created at: 2021-05-04 23:21:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/673#issuecomment-832310152  

Will do

---

## Comment 3

> Username: calvincramer  
> Created at: 2021-05-05 22:36:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/673#issuecomment-833094063  

I don't have permission to push my branch, so I can't create a pull request. Can I get permission? Or what should I do?  
  
In any event, here's the diff:  
```diff  
diff --git a/test/qi/binary.cpp b/test/qi/binary.cpp  
index 6fa5cdbf7..912d5b1c1 100644  
--- a/test/qi/binary.cpp  
+++ b/test/qi/binary.cpp  
@@ -97,7 +97,7 @@ int main()  
             qword(0x0102030405060708LL)));  
 #endif  
         BOOST_TEST(binary_test("\x3f\x80\x00\x00", 4, bin_float(1.0f)));  
-        BOOST_TEST(binary_test("\x3f\xf0\x00\x00\x00\x00\x00\x00", 8,  
+        BOOST_TEST(binary_test("\x3f\x80\x00\x00\x00\x00\x00\x00", 8,  
             bin_double(1.0)));  
 #endif  
     }  
diff --git a/test/x3/binary.cpp b/test/x3/binary.cpp  
index c614596bd..e0e4d27ed 100644  
--- a/test/x3/binary.cpp  
+++ b/test/x3/binary.cpp  
@@ -137,7 +137,7 @@ int main()  
             qword(0x0102030405060708LL)));  
 #endif  
         BOOST_TEST(binary_test("\x3f\x80\x00\x00", 4, bin_float(1.0f)));  
-        BOOST_TEST(binary_test("\x3f\xf0\x00\x00\x00\x00\x00\x00", 8,  
+        BOOST_TEST(binary_test("\x3f\x80\x00\x00\x00\x00\x00\x00", 8,  
             bin_double(1.0)));  
 #endif  
     }  
```  
  
I verified it passes on a Freescape P5020DS board (PPC big endian):  
```  
-> cmd  
[vxWorks *]# cd /romfs/  
[vxWorks *]# ls  
.  
..  
qi_binary.vxe  
x3_binary.vxe  
[vxWorks *]# ./qi_binary.vxe  
No errors detected.  
[vxWorks *]# ./x3_binary.vxe  
No errors detected.  
[vxWorks *]#  
```

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-05-05 22:43:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/673#issuecomment-833096701  

> I don't have permission to push my branch, so I can't create a pull request. Can I get permission? Or what should I do?  
  
Fork the repository, push the branch there, open a pull request against this repository. https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request  
  
PR 101 https://www.digitalocean.com/community/tutorials/how-to-create-a-pull-request-on-github

---

## Comment 5

> Username: calvincramer  
> Created at: 2021-05-06 00:30:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/673#issuecomment-833139913  

@Kojoley thanks for the direction, I really appreciate it. Especially considering this is my first time contributing to an open source project.  
Here's the PR: https://github.com/boostorg/spirit/pull/674
