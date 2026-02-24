# #119 - Null pointers lead to segfault using clang [Closed]

> Username: sgiraudot  
> Created at: 2018-09-24 12:23:54 UTC  
> Updated at: 2019-02-25 04:31:30 UTC  
> Closed at: 2019-02-25 04:31:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/119  

If I understand correctly, Boost Serialization is supposed to handle null pointers internally (handling them with a specific value and not calling `serialize()` on the dereferenced pointer).  
  
I found out that this doesn't work if I compile my program with `clang`.  
  
Consider the following program:  
  
```c++  
#include <iostream>  
#include <cstdlib>  
  
#include <boost/archive/text_iarchive.hpp>  
#include <boost/archive/text_oarchive.hpp>  
  
struct Node  
{  
  std::string name;  
  Node* next;  
  
  Node(const std::string& name)  
    : name (name), next(NULL)  
  { }  
  
  template <typename Archive>  
  void serialize(Archive& ar, unsigned /* version */)  
  {  
    ar & name;  
    ar & next;  
  }  
};  
  
int main()  
{  
  Node* first = new Node("first");  
  Node* second = new Node("second");  
  Node* last = new Node("last");  
  
  first->next = second;  
  second->next = last;  
  
  boost::archive::text_oarchive oa { std::cout };  
  oa << *first;  
  
  delete first;  
  delete second;  
  delete last;  
  
  return EXIT_SUCCESS;  
};  
```  
  
If I compile this with `g++` (version 6.4.0), I get the following output:  
```sh  
22 serialization::archive 15 1 0  
0 5 first 0  
1 6 second 0  
2 4 last -1  
```  
  
Which makes sense. If I compile the same code using `clang` (version 6.0.0), then I get the following output/error:  
```sh  
22 serialization::archive 15 1 0  
0 5 first 0  
1 6 second 0  
2 4 last 0  
zsh: segmentation fault (core dumped)  ./serialize_bug  
```  
  
It seems that the NULL pointer is not handled well and that it is dereferenced (leading to the segfault). The bug is still here if I use `nullptr` instead of `NULL`.  
  
The bug disappears for `clang` if I deactivate the optimizations (so I'm not exactly sure if this is a Boost bug or a CLang bug).

---

## Comment 1

> Username: robertramey  
> Created at: 2018-09-24 12:59:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-423966015  

OK - good test case.  We'll look into it.

---

## Comment 2

> Username: robertramey  
> Created at: 2018-10-03 17:08:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-426718252  

I've rebuilt and run this case with clang under Xcode on my MacOS system.  The output is what I would expect:  
  
`22 serialization::archive 17 1 0  
0 5 first 0  
1 6 second 0  
2 4 last -1  
Program ended with exit code: 0`  
  
Note the -1 which is the special tag for null pointers.  Your "correct" output doesn't have this.  Since I can't reproduce it on my own machine it's hard for me to track down  
  
BUT, it's pretty easier to use the debugger on your test case.  Set a trap to run to line 474 in oserializer.hpp.  This should go down to the point where it should be adding -1 to the archive.  Hopefully, you can see why it's not doing this. Note that I'm compiling for debug here.  I haven't tried release.   
  
I'm using clang:   
  
$clang -v  
Apple LLVM version 8.1.0 (clang-802.0.41)  
Target: x86_64-apple-darwin17.2.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
  
Let me know if you find out anything interesting.  
  
Robert Ramey

---

## Comment 3

> Username: pauldreik  
> Created at: 2018-10-05 18:34:28 UTC  
> Updated at: 2018-10-05 18:43:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-427459988  

This is reproducible for me too, both with gcc 6, clang 3.8, 3.9 and 6 on Debian Stretch.   
``g++-6 -std=c++14  x.cpp  -lboost_serialization -O3  -fsanitize=undefined && ./a.out``  
crashes with  
  
>  22 serialization::archive 14 1 0  
0 5 first 0  
1 6 second 0  
/usr/include/boost/archive/detail/oserializer.hpp:466:22: runtime error: reference binding to null pointer of type 'struct Node'  
2 4 last -1  
  
Doing the same thing with clang:  
 ``clang++-6.0 -std=c++14  x.cpp  -lboost_serialization -O3  -fsanitize=undefined && ./a.out``  
  
> 22 serialization::archive 14 1 0  
0 5 first 0  
1 6 second 0  
/usr/include/boost/archive/detail/oserializer.hpp:466:27: runtime error: reference binding to null pointer of type 'Node'  
2 4 last 0  
a.out: /usr/include/boost/archive/detail/oserializer.hpp:190: virtual void boost::archive::detail::pointer_oserializer<boost::archive::text_oarchive, Node>::save_object_ptr(boost::archive::detail::basic_oarchive &, const void *) const [Archive = boost::archive::text_oarchive, T = Node]: Assertion `__null != x' failed.  
3Avbruten (SIGABRT)

---

## Comment 4

> Username: pauldreik  
> Created at: 2018-10-05 21:06:45 UTC  
> Updated at: 2018-10-05 21:40:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-427498958  

I had a look at it in the debugger. It is this line 467 in oserializer.hpp (in boost 1.65):  
``register_type<decltype(*t)>(ar, * t);```  
where the dereference is made before the null check at the next line. In the example, t is null. In boost 1.68, register_type takes a pointer instead of a reference, so there is no null dereference.  
  
This was fixed a year ago in commit 480a8c94b00023baf109a74e433e15584a11d3a9 2017-09-20 by Daniel Arndt. This was included in boost 1.66.  
  
However, I can not find anything about this in the release notes: https://www.boost.org/doc/libs/1_68_0/libs/serialization/doc/index.html   
The release notes 1.66 through 1.68 all say "there are no known bugs".  
  
I think this is a quite serious issue - null dereference is undefined behaviour, after all.

---

## Comment 5

> Username: robertramey  
> Created at: 2018-10-10 03:47:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-428428496  

I was going to add in your test so squash this for all eternity.  I was going to rename it to test_null_ptr.  But I discovered I already have a test_null_ptr.    It looks to me that it should catch this error.  Which raises the question: Does the original test_null_ptr catch this or not?  If it does catch the error we needn't do anything at all.  If it doesn't catch the error, we should enhance the test to include the circumstance you've discovered.  
  
Robert Ramey

---

## Comment 6

> Username: pauldreik  
> Created at: 2018-10-10 07:53:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-428473833  

Using Boost 1.68 from Ubuntu official sources:  
The existing test test_null_ptr.cpp triggers the null pointer dereference at oserializer.hpp:467 which test_member_pointer_issue119.cpp was targeted to trigger. So yes, the existing test covers it.  
It actually had a look at test_null_ptr.cpp to see if it tested what I was after, but thought it used a derived class so it was after something else.  
  
Pinging @masterleinad, how was the issue detected in the first place? In case the existing test covered it, was this something only very recent releases of clang and gcc encounter?   
  
The suggested added test has some benefits. I want to add it, note replace the existing test!  
 - it is a simple and minimal reproducer of this bug report (#119), to refer to.  
 - it does not use a class hierarchy, like the existing test_null_ptr test, which shows there is a problem unrelated to the hierarchy.  
 - it increases the code coverage on oserializer.hpp: test_null_ptr uses 52 lines, test_member_pointer_issue119 uses 66 (measured with kcov)  
  
What about the addition to the release notes? Isn't it good to inform users that an upgrade is worth it?  
  
Thanks,  
Paul

---

## Comment 7

> Username: jhbrown42  
> Created at: 2018-11-30 18:14:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-443291415  

I ran into this using boost 1.61 and XCode 9.4.1.  It works in Debug mode (-O0), but not with any optimization (-O1 thru -O3).  I built a small test case and looked at the generated assembly.  As pauldreik pointed out, this happens in oserializer.hpp, where a pointer is dereferenced before it is checked for null.   
 With any level of optimization, the check for null is skipped, and the code that is executed is the same as if the pointer was not null.  I think the optimizer figures that the pointer cannot be null, because it is dereferenced to create a reference, and references should not be null.  
And as pauldreik pointed out, even with -O0 you can expose the problem with -fsanitize=undefined.  
I will grab 1.66 or later.  I also think the release notes should have indicated that this was fixed.

---

## Comment 8

> Username: robertramey  
> Created at: 2018-11-30 18:29:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-443295594  

> I also think the release notes should have indicated that this was fixed.  
probably, but I generally haven't updated release notes when I've fixed something.  Errors reports have not been that frequent and I've been lazy about it.  With the recent popularity of GitHub/PR system I'm now getting error reports for some very arcane corner cases.  In general it's a good thing. But it does create some more work.  In any case, I believe I fixed this but I don't remember when.  I locally run all the tests on Xcode and a recent version of GCC in both debug and release mode.  So I believe that that is fixed.  So could you investigate this on the most recently release boost version.  
  
The question of release notes is sort of interesting.  I'm thinking that perhaps I'd prefer using GitHub foo to produce a report of closed issues along with dates and comments.  This would be more effective than release notes and easier than updating separate documentation.  Github does keep all the information on the closed PR/complaints so I'm confident that it's doable.  But would require more GitHub foo than I currently have.  There's another question also that the Boost test matrix doesn't make very clear whether a test is being run in debug or release mode.  I think that the information is likely discoverable - but not that easily.  In any case the boost test matrix is also something beyond my poor powers of influence.

---

## Comment 9

> Username: jhbrown42  
> Created at: 2018-11-30 20:18:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-443327264  

I commend you for all your work, so I am not criticizing.  It must take a lot of time to respond.  I will just point out that I went to the boost.org site, and from looking at the version history, naively assumed that the serialization library had not been updated since 1.40.  I even checked the release notes for 1.68 and saw nothing to disabuse me of that notion.  I was unaware (at that time) that GitHub should have been my information source.

---

## Comment 10

> Username: rogerjarrett  
> Created at: 2018-12-12 18:29:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-446693211  

Defect is revealed when using Xcode 9.4.1 through Xcode 10.1 and I assume will continue to fail with Versions > 10.1.  Compiling with -O0 is a temporary workaround.  I am using Boost 1.65.1.

---

## Comment 11

> Username: robertramey  
> Created at: 2018-12-12 20:47:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-446738037  

Hmm - I test on xcode on my own desktop and I'm not seeing anything - is there as specific test in the test suite which fails?

---

## Comment 12

> Username: rogerjarrett  
> Created at: 2018-12-14 19:54:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-447436461  

Compile the attached program with Xcode 10  it will SEGV when using -01 or higher  (Xcode 9.4.1, Xcode 10.0, Xcode 10.1)  
[stackoverflow.cpp.gz](https://github.com/boostorg/serialization/files/2681557/stackoverflow.cpp.gz)  
  
Code from https://stackoverflow.com/questions/3457467/serialize-to-xml-using-boostserialization/13759467  
  
Program compiles and runs successfully when compiled with Xcode 9.0

---

## Comment 13

> Username: robertramey  
> Created at: 2018-12-14 22:59:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-447503908  

> This was fixed a year ago in commit 480a8c9 2017-09-20 by Daniel Arndt. This was included in boost 1.66.   
  
> Defect is revealed when using Xcode 9.4.1 through Xcode 10.1 and I assume will continue to fail with Versions > 10.1. Compiling with -O0 is a temporary workaround. I am using Boost 1.65.1.  
  
What happens when you test using boost 1.66 or better yet the most recent version of boost.

---

## Comment 14

> Username: rogerjarrett  
> Created at: 2018-12-20 20:50:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-449131194  

Confirmed fix  when applying change 480a8c9 to our boost sources.

---

## Comment 15

> Username: robertramey  
> Created at: 2019-02-24 19:05:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-466806061  

so may I close this now?

---

## Comment 16

> Username: jhbrown42  
> Created at: 2019-02-25 02:07:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/119#issuecomment-466847043  

Yes. It was fixed.   
  
Sent from my iPhone  
  
> On Feb 24, 2019, at 11:05 AM, Robert Ramey <notifications@github.com> wrote:  
>   
> so may I close this now?  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub, or mute the thread.
