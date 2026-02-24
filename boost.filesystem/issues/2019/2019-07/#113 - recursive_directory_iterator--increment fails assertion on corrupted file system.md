# #113 - recursive_directory_iterator::increment fails assertion on corrupted file system [Closed]

> Username: xRodney  
> Created at: 2019-07-16 10:02:37 UTC  
> Updated at: 2019-10-03 07:20:46 UTC  
> Closed at: 2019-08-02 17:02:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113  

From operations.cpp  
```  
  // Invariant: On return, the top of the iterator stack is the next valid (possibly  
  // end) iterator, regardless of whether or not an error is reported, and regardless of  
  // whether any error is reported by exception or error code. In other words, progress  
  // is always made so a loop on the iterator will always eventually terminate  
  // regardless of errors.  
  BOOST_FILESYSTEM_DECL  
  void recur_dir_itr_imp::increment(system::error_code* ec)  
```  
  
This invariant is not honored on corrupted filesystems, when a nested directory cannot be traversed fully.  
An example of such "corrupted" filesystem is Apple implementation of quarantine on Mac OS X High Sierra and higher.  
  
### Steps to reproduce  
  
Consider this program:  
```  
#define BOOST_FILESYSTEM_VERSION 3  
  
//  As an example program, we don't want to use any deprecated features  
#ifndef BOOST_FILESYSTEM_NO_DEPRECATED   
#  define BOOST_FILESYSTEM_NO_DEPRECATED  
#endif  
#ifndef BOOST_SYSTEM_NO_DEPRECATED   
#  define BOOST_SYSTEM_NO_DEPRECATED  
#endif  
  
#include "boost/filesystem/operations.hpp"  
#include "boost/filesystem/path.hpp"  
#include "boost/progress.hpp"  
#include <iostream>  
  
namespace fs = boost::filesystem;  
  
int main(int argc, char* argv[])  
{  
  fs::path p(fs::current_path());  
  
  if (argc > 1)  
    p = fs::system_complete(argv[1]);  
  else  
    std::cout << "\nusage:   recursive_ls [path]" << std::endl;  
  
  unsigned long file_count = 0;  
  unsigned long dir_count = 0;  
  unsigned long other_count = 0;  
  unsigned long err_count = 0;  
  
  if (!fs::exists(p))  
  {  
    std::cout << "\nNot found: " << p << std::endl;  
    return 1;  
  }  
  
  if (fs::is_directory(p))  
  {  
    boost::system::error_code ec;  
    std::cout << "\nIn directory: " << p << "\n\n";  
    fs::recursive_directory_iterator end_iter;  
    for (fs::recursive_directory_iterator dir_itr(p);  
          dir_itr != end_iter;)  
    {  
      try  
      {  
        if (fs::is_directory(dir_itr->status()))  
        {  
          ++dir_count;  
          std::cout << dir_itr->path() << " [directory]\n";  
        }  
        else if (fs::is_regular_file(dir_itr->status()))  
        {  
          ++file_count;  
          std::cout << dir_itr->path() << "\n";  
        }  
        else  
        {  
          ++other_count;  
          std::cout << dir_itr->path() << " [other]\n";  
        }  
  
        boost::system::error_code ec;  
        dir_itr.increment(ec);  
        if (ec)  
        {  
            ++err_count;  
            std::cout << "*" << dir_itr->path() << " *" << ec.message() << std::endl;  
        }  
      }  
      catch (const std::exception & ex)  
      {  
        ++err_count;  
        std::cout << dir_itr->path() << " " << ex.what() << std::endl;  
      }  
    }  
    std::cout << "\n" << file_count << " files\n"  
              << dir_count << " directories\n"  
              << other_count << " others\n"  
              << err_count << " errors\n";  
  }  
  else // must be a file  
  {  
    std::cout << "\nFound: " << p << "\n";      
  }  
  return 0;  
}  
  
  
```  
  
## 1. Real world example (Mac OS X High Sierra)  
  
1. Download terminal application from here: https://www.iterm2.com (In theory, any app will do)  
2. Do not install it!  
3. Just run the downloaded file from whenever it was downloaded to  
4. I repeat, do not let MacOS move it to /Applications or anywhere else  
5. Check that `mount` command gives you something like:   
```  
/Users/<user>/Downloads/iTerm.app on /private/var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/317B211C-C40C-4662-8947-416AC32DD07B (nullfs, local, nodev, nosuid, read-only, nobrowse, mounted by iscan)  
```  
6. Leave the app running  
7. `./recursive_ls /var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/`    (ids may vary, but the path must end with AppTranslocation)  
  
  
Output:  
  
```  
  
In directory: "/var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/"  
  
"/var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/317B211C-C40C-4662-8947-416AC32DD07B" [directory]  
"/var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/317B211C-C40C-4662-8947-416AC32DD07B/d" [directory]  
"/var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/317B211C-C40C-4662-8947-416AC32DD07B/d/iTerm.app" [directory]  
  
... shortened ...  
  
"/var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/317B211C-C40C-4662-8947-416AC32DD07B/d/iTerm.app/Contents/Frameworks/ColorPicker.framework/ColorPicker"  
"/var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/317B211C-C40C-4662-8947-416AC32DD07B/d/iTerm.app/Contents/Info.plist"  
"/var/folders/ys/08961svn0qx6_1g9sg6j32sw0000gn/T/AppTranslocation/317B211C-C40C-4662-8947-416AC32DD07B/d/iTerm.app/Contents/PkgInfo"  
Assertion failed: ((m_imp.get())&&("attempt to dereference end iterator")), function dereference, file ../../../boost/filesystem/operations.hpp, line 1013.  
*Abort trap: 6  
```  
  
## 2. Artifical example (any linux or MAC with FUSE)  
  
I have been able to reproduce this behaviour relatively closely with [fusepy](https://github.com/fusepy/fusepy) using the attached script.  
[memoryfs.py.txt](https://github.com/boostorg/filesystem/files/3402097/memoryfs.py.txt)  
  
Python3 and pip is required  
```  
sudo -s                               # Switch to root  
pip3 install fusepy  
python3 memoryfs.py mountpoint/ &     # The file memoryfs.py must be downloaded to the current directory  
mkdir -p mountpoint/dirxx/dir2  
mkdir -p mountpoint/diryy/dir2  
./recursive_ls  
```  
  
Output:  
```  
  
usage:   recursive_ls [path]  
  
In directory: "/home/rodney/Projects/boost/libs/filesystem/test"  
  
"/home/rodney/Projects/boost/libs/filesystem/test/mountpoint" [directory]  
"/home/rodney/Projects/boost/libs/filesystem/test/mountpoint/dirxx" [directory]  
*"/home/rodney/Projects/boost/libs/filesystem/test/mountpoint/dirxx" *Input/output error  
"/home/rodney/Projects/boost/libs/filesystem/test/mountpoint/dirxx" [directory]  
recursive_ls: ../../../boost/filesystem/operations.hpp:1013: boost::filesystem::directory_entry& boost::filesystem::directory_iterator::dereference() const: Assertion `(m_imp.get())&&("attempt to dereference end iterator")' failed.  
Aborted  
  
```  
  
  
### Analysis  
  
In both cases, the opendir passes and readdir (or readdir_r) provides first few entries of the nested directory. After a few entries, readdir(_r) fails with an error.  
  
The following condition gets activated:  
```  
void directory_iterator_increment(directory_iterator& it,  
    system::error_code* ec)  
	  
	/* .... */  
	  
	 if (increment_ec)  // happens if filesystem is corrupt, such as on a damaged optical disc  
        {  
          boost::intrusive_ptr< detail::dir_itr_imp > imp;  
          imp.swap(it.m_imp);  
          path error_path(imp->dir_entry.path().parent_path());  // fix ticket #5900  
          if (ec == 0)  
            BOOST_FILESYSTEM_THROW(  
              filesystem_error("boost::filesystem::directory_iterator::operator++",  
                error_path,  
                increment_ec));  
          *ec = increment_ec;  
          return;  
        }  
```  
  
which is then handled here:  
  
```  
  void recur_dir_itr_imp::increment(system::error_code* ec)  
  {  
    /* ..... */  
  
    //  Do the actual increment operation on the top iterator in the iterator  
    //  stack, popping the stack if necessary, until either the stack is empty or a  
    //  non-end iterator is reached.  
    while (!m_stack.empty())  
    {  
      directory_iterator& it = m_stack.top();  
      detail::directory_iterator_increment(it, ec);  
      if (ec && *ec)     /// <<<<<----------------- here  
        return;  
      if (it != directory_iterator())  
        break;  
  
      m_stack.pop();  
      --m_level;  
    }  
```  
  
`it` equals `directory_iterator()` at the marked point, but we return too early to progress to another (possibly valid) directory.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-07-16 10:53:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-511767537  

I haven't looked into the issue, but since you're working on it, did you consider what the C++ Standard says about it? Out of all possible solutions, I think the standard behavior would be more preferable.

---

## Comment 2

> Username: xRodney  
> Created at: 2019-07-17 14:01:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-512267621  

I have been able find some information about the other issue I reported (#112) in the draft of the standard (http://eel.is/c++draft/fs.class.rec.dir.itr#fs.rec.dir.itr.members)  
  
I have found this https://en.cppreference.com/w/cpp/filesystem/recursive_directory_iterator  
> If the recursive_directory_iterator reports an error or is advanced past the last directory entry of the top-level directory, it becomes equal to the default-constructed iterator, also known as the end iterator.   
  
This is not in the standard (at least in the draft I referred to) and I would find this behaviour unfortunate as it prevents the user to recover from the error in any way.

---

## Comment 3

> Username: xRodney  
> Created at: 2019-07-17 14:10:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-512271616  

Speaking of recovery, I have suggested two separate means of recovery in my PR https://github.com/boostorg/filesystem/pull/114.   
  
The differentiate between where the error occurs:  
* If opendir raises an error (such as Permission denied), the recovery is to disable recursion to this directory  
* If readdir raises an error (such as Input/output error or Result too large on Apple), the recovery involves popping that directory and then disabling recursion back there  
  
That means that the caller has little control over the recovery, because from the error_code it may not be evident which function failed, and thus they do not know if they should pop the current dir or not

---

## Comment 4

> Username: Lastique  
> Created at: 2019-07-31 15:54:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-516910150  

I think the current standard draft (N4820) is pretty clear on the intended behavior, and I think that behavior is sane. Specifically:  
  
- If `directory_options::skip_permission_denied` option was specified on iterator construction and an access denied condition was detected (which happens on `opendir`) then that subdirectory is skipped as if it's empty.  
- On any other errors indicate failure (in case of increment - throw an exception).  
  
In the latter case the user can try to recover by manually calling `depth` and `pop` on the iterator. It is not the iterator's job to attempt to conceal or recover from errors such as this. Side effects like returning the same directory twice are not acceptable. In other words, `readdir` always means an exception, this part is not going to change.  
  
Currently, there is no `directory_options` in Boost.Filesystem, `symlink_option` is used instead and it doesn't have an equivalent of `skip_permission_denied`. I think, we should add `directory_options`, including the `skip_permission_denied`, and deprecate `symlink_option`.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-07-31 16:28:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-516922935  

> In the latter case the user can try to recover by manually calling `depth` and `pop` on the iterator.  
  
Upon reading the standard further it seems like this recovery is not allowed since the iterator is required to become equal to the end iterator on error. This follows from [fs.class.rec.dir.itr]/3 and [fs.class.directory.iterator]/3.

---

## Comment 6

> Username: Lastique  
> Created at: 2019-08-02 17:10:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-517777906  

See if the new option `pop_on_error` from https://github.com/boostorg/filesystem/commit/9a14c37d6f954c371ab1f6d78e9445417a206545 suits your needs.

---

## Comment 7

> Username: nishaparakkat  
> Created at: 2019-10-01 10:00:37 UTC  
> Updated at: 2019-10-01 10:01:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-536964217  

Hi, I took latest boost  and filesystem submodule shows latest commit as f795041.   
The fix in  9a14c37 is already present in the git tree.  
  
I tried to check if the reproduction case mentioned in the above comments using fusepy is fixed.   
But I get the below error  
  
my-ThinkStation-P910:/myhome/mytest/boost/run_bugboost_171/from_site_113$ sudo -s  
root@my-ThinkStation-P910:/myhome/mytest/boost/run_bugboost_171/from_site_113# python3 memoryfs.py mountpoint/ &  
[1] 1136  
root@my-ThinkStation-P910:/myhome/mytest/boost/run_bugboost_171/from_site_113# mkdir -p mountpoint/dirxx/dir2  
root@my-ThinkStation-P910:/myhome/mytest/boost/run_bugboost_171/from_site_113# mkdir -p mountpoint/diryy/dir2  
root@my-ThinkStation-P910:/myhome/mytest/boost/run_bugboost_171/from_site_113# ./recursive_ls_171   
  
usage:   recursive_ls [path]  
  
In directory: "/myhome/mytest/boost/run_bugboost_171/from_site_113"  
  
"/myhome/mytest/boost/run_bugboost_171/from_site_113/recursive_ls.cpp"  
"/myhome/mytest/boost/run_bugboost_171/from_site_113/bugboost_171_stdc++11"  
"/myhome/mytest/boost/run_bugboost_171/from_site_113/mountpoint" [directory]  
"/myhome/mytest/boost/run_bugboost_171/from_site_113/mountpoint/diryy" [directory]  
recursive_ls_171: /myhome/mytest/boost/boostgit/boost_lib_1.71/include/boost/filesystem/directory.hpp:556: boost::filesystem::directory_entry& boost::filesystem::recursive_directory_iterator::dereference() const: Assertion `(!is_end())&&("dereference of end recursive_directory_iterator")' failed.  
Aborted (core dumped)  
  
I built the recursive_ls using below :  
g++ -std=c++17 -Os -Wall -pedantic recursive_ls.cpp -lboost_system -lboost_filesystem -lpthread -lboost_thread -L/myhome/mytest/boost/run_bugboost_171/from_site_113boost_lib_1.71/lib -L /usr/lib -I/myhome/mytest/boost/run_bugboost_171/from_site_113/boost_lib_1.71/include/ -o run_bugboost_171_17  
  
tried a version with -std=c++11 too but i get the same error.  
  
Is this issue fixed ? or am i missing something here?

---

## Comment 8

> Username: nishaparakkat  
> Created at: 2019-10-02 11:24:04 UTC  
> Updated at: 2019-10-02 11:25:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-537449035  

#113 #hi all,   
if I change the recursive_ls.cpp that was used for reproducing the issue, so that   
   
```  
 for (fs::recursive_directory_iterator dir_itr(p);  
          dir_itr != end_iter;)  
```   
  
  becomes  
```  
for (fs::recursive_directory_iterator dir_itr(p);  
          dir_itr != end_iter;++dir_itr)  
```  
    
  
and I remove the lines   
```  
boost::system::error_code ec;  
dir_itr.increment(ec);  
```  
  
Then I get the below error.  
  
  
usage:   recursive_ls [path]  
  
In directory: "/myhome/mytest/boost/run_bugboost_171/from_site_113"  
  
"/myhome/mytest/boost/run_bugboost_171/from_site_113/recursive_ls.cpp"  
"/myhome/mytest/boost/run_bugboost_171/from_site_113/run_bugboost_171_17_no_except"  
"/myhome/mytest/boost/run_bugboost_171/from_site_113/bugboost_171_original"  
"/myhome/mytest/boost/run_bugboost_171/from_site_113/mountpoint" [directory]  
terminate called after throwing an instance of 'boost::filesystem::filesystem_error'  
**what():  filesystem::recursive_directory_iterator increment error: Input/output error  
Aborted (core dumped)**  
  
This issue is seen for our software that uses a boost version 1.65 too.   
Is this a known issue?

---

## Comment 9

> Username: Lastique  
> Created at: 2019-10-03 07:14:06 UTC  
> Updated at: 2019-10-03 07:20:46 UTC  
> Url: https://github.com/boostorg/filesystem/issues/113#issuecomment-537819199  

The test code in the OP is incorrect because it dereferences the iterator in `std::cout` lines after an operation failed on the iterator. The iterator becomes singular (i.e. equal to end) on error.  
  
> Is this a known issue?  
  
I'm not a Python expert, but I think `memoryfs.py` is intended to simulate a corrupted filesystem. Thus the failure on iteration.
