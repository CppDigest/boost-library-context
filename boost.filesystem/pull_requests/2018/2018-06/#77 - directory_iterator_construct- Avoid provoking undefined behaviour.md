# #77 directory_iterator_construct: Avoid provoking undefined behaviour [Merged]

> Username: mikecrowe  
> Created at: 2018-06-29 15:09:53 UTC  
> Updated at: 2019-01-14 07:16:10 UTC  
> Merged at: 2018-09-06 01:27:21 UTC  
> Closed at: 2018-09-06 01:27:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77  

The directory_iterator(const path& p) constructor calls  
detail::directory_iterator_construct passing nullptr (as 0) for the ec  
parameter.  
  
detail::directory_iterator_construct may call  
directory_iterator::increment(*ec) which dereferences the nullptr provoking  
undefined behaviour.  
  
On GCC, and probably many other compilers, it seems that this merely causes  
a null reference to be passed to directory_iterator::increment which in  
turn, turns it back into nullptr when calling  
detail::directory_iterator_increment which knows how to deal with the  
nullptr and is happy.  
  
Unfortunately, directory_iterator::increment(system::error_code& ec) is  
marked noexcept (unlike the version that takes no arguments) but  
detail::directory_iterator_increment throws exceptions when ec == nullptr.  
This results in std::terminate being called.  
  
The simplest way to fix this is for detail::directory_iterator_construct to  
just pass on the potentially-nullptr ec argument to  
detail::directory_iterator_increment rather than going via the  
potentially-noexcept directory_iterator::increment member function.  
  
(Discovered in the field with Boost 1.63 and a faulty disk. I managed to  
reproduce the problem by teaching dir_itr_increment to pretend that  
readdir_r_simulator returned an error when it saw a certain filename.)

---

## Comment 1

> Username: mikecrowe  
> Created_at: 2018-06-29 15:10:57 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-401384263  

I've reproduced the same problem and confirmed the fix with boost 1.66 too. Unfortunately I was unable to get the current state of Boost master to compile in order to run the test suite there. Hopefully travis/appveyor will tell me if I've broken anything.

---

## Comment 2

> Username: mikecrowe  
> Created_at: 2018-07-02 09:29:05 UTC  
> Updated_at: 2018-08-09 19:03:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-401732268  

The AppVeyor test failure appears to be:  
  
> libs\filesystem\test\operations_test.cpp(1394): test 'fs::exists(link)' failed in function 'void __cdecl `anonymous-namespace'::remove_symlink_tests(void)'  
  
The remove_symlink_tests test doesn't even seem to use directory_iterator, so it looks like this failure may not be related to my patch.

---

## Comment 3

> Username: mikecrowe  
> Created_at: 2018-08-09 19:03:29 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-411863179  

Is there anything else that I need to do? Is my explanation not clear?

---

## Comment 4

> Username: b-spencer  
> Created_at: 2018-09-06 13:16:25 UTC  
> Updated_at: 2018-09-06 13:16:52 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-419088972  

This fixes the second issue of #53, fixed by aa2a58a4e63f7c433423c17546bc738c9a46de22.  But, the first issue in #53, fixed by 1303b3ed7d731dac4754eb3a6e1a370b9cf985dc, still seems outstanding.  I will look at rebasing #53.

---

## Comment 5

> Username: killerbot242  
> Created_at: 2019-01-09 11:55:29 UTC  
> Updated_at: 2019-01-09 11:56:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-452671190  

This change might be the cause of a new problem.  
Consider the following code snippet :   
`#include <boost/filesystem.hpp>  
#include <string>  
  
int main()  
{  
    const std::string path{"/tmp"};  
  
    boost::filesystem::directory_iterator iter(path);  
    while (iter != boost::filesystem::directory_iterator()) {  
        ++iter;  
    }  
    return 0;  
}`  
  
this now gives rise to complains when this is ran with valgrind :  
`<error>  
  <unique>0x0</unique>  
  <tid>1</tid>  
  <kind>UninitCondition</kind>  
  <what>Conditional jump or move depends on uninitialised value(s)</what>  
  <stack>  
    <frame>  
      <ip>0x4E46531</ip>  
      <obj>/home/ldco/boost/lib/libboost_filesystem.so.1.69.0</obj>  
      <fn>boost::filesystem::detail::directory_iterator_increment(boost::filesystem::directory_iterator&amp;, boost::system::error_code*)</fn>  
    </frame>  
    <frame>  
      <ip>0x4E4804A</ip>  
      <obj>/home/ldco/boost/lib/libboost_filesystem.so.1.69.0</obj>  
      <fn>boost::filesystem::detail::directory_iterator_construct(boost::filesystem::directory_iterator&amp;, boost::filesystem::path const&amp;, boost::system::error_code*)</fn>  
    </frame>  
    <frame>  
      <ip>0x405D12</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>boost::filesystem::directory_iterator::directory_iterator(boost::filesystem::path const&amp;)</fn>  
      <dir>/home/ldco/boost/include/boost/filesystem</dir>  
      <file>operations.hpp</file>  
      <line>905</line>  
    </frame>  
    <frame>  
      <ip>0x40409D</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>main</fn>  
      <dir>/home/ldco/Projects/TryOuts/FileSystem0/src</dir>  
      <file>main.cpp</file>  
      <line>8</line>  
    </frame>  
  </stack>  
  <auxwhat>Uninitialised value was created by a heap allocation</auxwhat>  
  <stack>  
    <frame>  
      <ip>0x4C2E01F</ip>  
      <obj>/usr/lib64/valgrind/vgpreload_memcheck-amd64-linux.so</obj>  
      <fn>malloc</fn>  
    </frame>  
    <frame>  
      <ip>0x4E47DE9</ip>  
      <obj>/home/ldco/boost/lib/libboost_filesystem.so.1.69.0</obj>  
      <fn>boost::filesystem::detail::directory_iterator_construct(boost::filesystem::directory_iterator&amp;, boost::filesystem::path const&amp;, boost::system::error_code*)</fn>  
    </frame>  
    <frame>  
      <ip>0x405D12</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>boost::filesystem::directory_iterator::directory_iterator(boost::filesystem::path const&amp;)</fn>  
      <dir>/home/ldco/boost/include/boost/filesystem</dir>  
      <file>operations.hpp</file>  
      <line>905</line>  
    </frame>  
    <frame>  
      <ip>0x40409D</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>main</fn>  
      <dir>/home/ldco/Projects/TryOuts/FileSystem0/src</dir>  
      <file>main.cpp</file>  
      <line>8</line>  
    </frame>  
  </stack>  
</error>  
  
<error>  
  <unique>0x3</unique>  
  <tid>1</tid>  
  <kind>UninitCondition</kind>  
  <what>Conditional jump or move depends on uninitialised value(s)</what>  
  <stack>  
    <frame>  
      <ip>0x4E46531</ip>  
      <obj>/home/ldco/boost/lib/libboost_filesystem.so.1.69.0</obj>  
      <fn>boost::filesystem::detail::directory_iterator_increment(boost::filesystem::directory_iterator&amp;, boost::system::error_code*)</fn>  
    </frame>  
    <frame>  
      <ip>0x405D70</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>boost::filesystem::directory_iterator::increment()</fn>  
      <dir>/home/ldco/boost/include/boost/filesystem</dir>  
      <file>operations.hpp</file>  
      <line>941</line>  
    </frame>  
    <frame>  
      <ip>0x407357</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>void boost::iterators::iterator_core_access::increment&lt;boost::filesystem::directory_iterator&gt;(boost::filesystem::directory_iterator&amp;)</fn>  
      <dir>/home/ldco/boost/include/boost/iterator</dir>  
      <file>iterator_facade.hpp</file>  
      <line>556</line>  
    </frame>  
    <frame>  
      <ip>0x406947</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>boost::iterators::detail::iterator_facade_base&lt;boost::filesystem::directory_iterator, boost::filesystem::directory_entry, boost::iterators::single_pass_traversal_tag, boost::filesystem::directory_entry&amp;, long, false, false&gt;::operator++()</fn>  
      <dir>/home/ldco/boost/include/boost/iterator</dir>  
      <file>iterator_facade.hpp</file>  
      <line>666</line>  
    </frame>  
    <frame>  
      <ip>0x4040E6</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>main</fn>  
      <dir>/home/ldco/Projects/TryOuts/FileSystem0/src</dir>  
      <file>main.cpp</file>  
      <line>10</line>  
    </frame>  
  </stack>  
  <auxwhat>Uninitialised value was created by a heap allocation</auxwhat>  
  <stack>  
    <frame>  
      <ip>0x4C2E01F</ip>  
      <obj>/usr/lib64/valgrind/vgpreload_memcheck-amd64-linux.so</obj>  
      <fn>malloc</fn>  
    </frame>  
    <frame>  
      <ip>0x4E47DE9</ip>  
      <obj>/home/ldco/boost/lib/libboost_filesystem.so.1.69.0</obj>  
      <fn>boost::filesystem::detail::directory_iterator_construct(boost::filesystem::directory_iterator&amp;, boost::filesystem::path const&amp;, boost::system::error_code*)</fn>  
    </frame>  
    <frame>  
      <ip>0x405D12</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>boost::filesystem::directory_iterator::directory_iterator(boost::filesystem::path const&amp;)</fn>  
      <dir>/home/ldco/boost/include/boost/filesystem</dir>  
      <file>operations.hpp</file>  
      <line>905</line>  
    </frame>  
    <frame>  
      <ip>0x40409D</ip>  
      <obj>/home/ldco/Projects/TryOuts/FileSystem0/Deliv/Debug/FileSystem0-Paths</obj>  
      <fn>main</fn>  
      <dir>/home/ldco/Projects/TryOuts/FileSystem0/src</dir>  
      <file>main.cpp</file>  
      <line>8</line>  
    </frame>  
  </stack>  
</error>  
`  
  
  
  
gcc : gcc (SUSE Linux) 7.3.1 20180323 [gcc-7-branch revision 258812]  
valgrind : valgrind-3.13.0  
  
===> so conditional jump based on uninitialized values ==> that sounds like undefined behaviour ?

---

## Comment 6

> Username: Lastique  
> Created_at: 2019-01-09 14:03:03 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-452706742  

Should be fixed in bbe9d17, thanks.

---

## Comment 7

> Username: mikecrowe  
> Created_at: 2019-01-09 14:06:31 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-452707792  

> This change might be the cause of a new problem.  
> Consider the following code snippet :  
> `#include <boost/filesystem.hpp>  
> #include  
>   
> int main()  
> {  
> const std::string path{"/tmp"};  
>   
> ```  
> boost::filesystem::directory_iterator iter(path);  
> while (iter != boost::filesystem::directory_iterator()) {  
>     ++iter;  
> }  
> return 0;  
> ```  
> }`  
  
That code runs fine for me under valgrind with boost 1.63 + my patch (which is what I did most of my testing on) and boost 1.66 + my patch (which is what I'm currently shipping.) I haven't upgraded to anything newer yet.  
  
I suspect that your problem is not related to this pull request.  
  
Mike.

---

## Comment 8

> Username: killerbot242  
> Created_at: 2019-01-09 14:26:45 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-452714498  

the problem I mentioned occurs in boost 1.69, and was still working ok in boost 1.68, the fact I ended up in this ticket is due to the release notes : Fixed undefined behavior in boost::filesystem::directory_iterator implementation. (PR#77)   
That's how I made the link, I will past my boost install with the fix mentioned above, and report back.

---

## Comment 9

> Username: Lastique  
> Created_at: 2019-01-09 14:27:36 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-452714773  

Yes, it is not related to this PR. The problem was present in code since ba4aaf30, which is the initial release of Boost.Filesystem v3. It did not reproduce on Linux before Boost 1.69 (more precisely, until 48b8d753) because a different code branch was used on Linux. The problem could reproduce on other platforms before that revision.

---

## Comment 10

> Username: killerbot242  
> Created_at: 2019-01-10 07:58:10 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-453003326  

I have patched my boost environment with the mentioned fix above, and can confirm that valgrind is happy again :-)

---

## Comment 11

> Username: killerbot242  
> Created_at: 2019-01-14 07:16:10 UTC  
> Url: https://github.com/boostorg/filesystem/pull/77#issuecomment-453917440  

I have patched my boost environment with the mentioned fix above, and can confirm that valgrind is happy again :-)

---
