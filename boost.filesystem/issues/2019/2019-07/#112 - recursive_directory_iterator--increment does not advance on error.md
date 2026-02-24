# #112 - recursive_directory_iterator::increment does not advance on error [Closed]

> Username: xRodney  
> Created at: 2019-07-12 15:36:10 UTC  
> Updated at: 2019-08-02 17:02:49 UTC  
> Closed at: 2019-08-02 17:02:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/112  

I'm using the latest develop branch (will be 1.71)  
  
From operations.cpp:  
```  
  // Invariant: On return, the top of the iterator stack is the next valid (possibly  
  // end) iterator, regardless of whether or not an error is reported, and regardless of  
  // whether any error is reported by exception or error code. In other words, progress  
  // is always made so a loop on the iterator will always eventually terminate  
  // regardless of errors.  
  BOOST_FILESYSTEM_DECL  
  void recur_dir_itr_imp::increment(system::error_code* ec)  
```  
  
This is not (always?) true as the iterator does not advance when an error is reported during push_directory.  
  
### Steps to reproduce  
Consider this program (slightly modified simple_ls.cpp from examples)  
  
recursive_ls.cpp  
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
  
Now run the following commands (tested on linux):  
```  
mkdir testdir  
chmod 000 testdir  
mkdir otherdir  
./recursive_ls  
```  
  
### Actual result  
```  
usage:   recursive_ls [path]  
  
In directory: "/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test"  
  
"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" [directory]  
*"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" *Permission denied  
"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" [directory]  
*"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" *Permission denied  
"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" [directory]  
*"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" *Permission denied  
"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" [directory]  
*"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" *Permission denied  
"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" [directory]  
*"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" *Permission denied  
  
... infinite loop  
```  
  
### Expected result  
One of:  
1. Fullfill the invariant and make the iterator always advance. I can see that other tests expect that the for loop finishes without special handling or errors  
2. Drop the invariant and write into documentation that on errors, `it.no_push()` must be called in order to continue.  
  
I'm not sure what is better.   
  
The first would have the disadvantage that because the iterator has already advances, the current path would not correspond to the error code, like this:  
```  
"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/testdir" [directory]  
*"/home/rodney/Projects/boost/boost/libs/filesystem/test/recursive_test/otherdir" *Permission denied  
```  
This is because testdir record is read and reported just fine, the error only happens once we try to recurse into that directory. So the contents are skipped and iterator now points to the next record in the parent directory.  
  
The second variant, on the other hand, only works for errors connected to directory opens. What about other errors? Should another api be added to "acknowledge" the current error?  
  
Many thanks for a wonderful library.

---

## Comment 1

> Username: xRodney  
> Created at: 2019-07-15 07:40:55 UTC  
> Updated at: 2019-07-15 12:01:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/112#issuecomment-511302278  

There is a third variant, which is very simple to fix and may be enough to break the loop:  
3. When it is not possible to descend into a directory, raise the error as of now, but call "no_push" implicitly.  
  
The fix is very simple:  
  
```  
inline  
void recur_dir_itr_imp::increment(system::error_code* ec)  
    // ec == 0 means throw on error  
    //  
    // Invariant: On return, the top of the iterator stack is the next valid (possibly  
    // end) iterator, regardless of whether or not an error is reported, and regardless of  
    // whether any error is reported by exception or error code. In other words, progress  
    // is always made so a loop on the iterator will always eventually terminate  
    // regardless of errors.  
    {  
      system::error_code ec_push_directory;  
  
      //  if various conditions are met, push a directory_iterator into the iterator stack  
      if (push_directory(ec_push_directory))  
      {  
        if (ec)  
          ec->clear();  
        return;  
      }  
  
      // report errors if any  
      if (ec_push_directory)  
      {  
        m_options |= static_cast< unsigned int >(symlink_option::_detail_no_push);   // <<< this line is new, equivalent of no_push()  
        if (ec)  
        {  
          *ec = ec_push_directory;  
          return;  
        }  
        else  
        {  
          BOOST_FILESYSTEM_THROW(filesystem_error(  
            "filesystem::recursive_directory_iterator directory error",  
            ec_push_directory));  
        }  
      }  
```  
  
In my case, this will print the following output:  
```  
usage:   recursive_ls [path]  
  
In directory: "/home/rodney/Projects/boost/libs/filesystem/test/recursive_test"  
  
"/home/rodney/Projects/boost/libs/filesystem/test/rectest/otherdir" [directory]  
"/home/rodney/Projects/boost/libs/filesystem/test/rectest/testdir" [directory]  
*"/home/rodney/Projects/boost/libs/filesystem/test/rectest/testdir" *Permission denied  
"/home/rodney/Projects/boost/libs/filesystem/test/rectest/testdir" [directory]  
  
0 files  
3 directories    <<<<<<< this is wrong  
0 others  
1 errors  
  
```  
  
We can see that the record for testdir is printed twice: Once when the directory record is first discovered in the parent directory and once (with an error before it) when it is to be opened. I find it ok for these reasons:  
* The dir cannot be opened in the first invocation, the user must have the option of calling no_push on any dir, thus preventing it from opening.  
* The returned error code now matches the current path() after the second invocation  
* The endless loop is still broken and the iterator still "advances" its internal state, even though externally it still points to the same path. In a broad sense, the invariant is honored.  
* In my testing, I have run into situations when a single increment is to raise multiple errors before it can finally advance to the next valid path. The proposed pattern can accommodate also for these situations.

---

## Comment 2

> Username: xRodney  
> Created at: 2019-07-17 13:54:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/112#issuecomment-512264750  

The C++ standard mentions "Permission denied" error specifically here: http://eel.is/c++draft/fs.rec.dir.itr.members#23 It is configurable with an option, whether the directory we do not have access for should be silently skipped, or if an error should be raised.  
  
If an error is to be raised, however, AFAIK the behaviour is unspecified whether the iterator should progress or not. I have only been able to find this general note: http://eel.is/c++draft/fs.err.report#3  
  
> Functions having an argument of type error_­code& handle errors as follows, unless otherwise specified:  
> If a call by the implementation to an operating system or other underlying API results in an error that prevents the function from meeting its specifications, the error_­code& argument is set as appropriate for the specific operating system dependent error. Otherwise, clear() is called on the error_­code& argument.
