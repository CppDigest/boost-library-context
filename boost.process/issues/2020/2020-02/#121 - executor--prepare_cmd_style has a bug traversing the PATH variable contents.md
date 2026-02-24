# #121 - executor::prepare_cmd_style has a bug traversing the PATH variable contents [Closed]

> Username: alloutlife  
> Created at: 2020-02-12 08:03:06 UTC  
> Updated at: 2020-11-03 10:57:04 UTC  
> Closed at: 2020-11-03 10:57:04 UTC  
> Url: https://github.com/boostorg/process/issues/121  

Splitting the PATH environ variable by a colons we have a path array containing the directories with the first entry erroneously preceded by a substring 'PATH='. If an executable invoked by boost::process::system( "<exe>" ) or boost::process:child( "<exe>") is expected to be located in that first PATH entry then executor::prepare_cmd_style() fails to build a full path to the executable. And this leads to process execution error.  
  
In my example i have a "PATH=/usr/bin:/bin" and boost::process::child{ "wget" } fails with "No such file or directory" error because wget is located in /usr/bin.  
  
The solution is to split the contents of the PATH variable not the full string "PATH=.."  
The possible fix is to advance the *e pointer when boost::split() is invoked.  
  
original [boost/process/detail/posix/executor.hpp]:  
    boost::split(path, *e, boost::is_any_of(":"));  
change to:  
    boost::split(path, *e + 5, boost::is_any_of(":"));  
  
    , where 5 is the length of the "PATH=".

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-02-12 08:34:18 UTC  
> Url: https://github.com/boostorg/process/issues/121#issuecomment-585091344  

This is intended, you need to explictly invoke `search_path`.

---

## Comment 2

> Username: alloutlife  
> Created at: 2020-02-12 09:21:02 UTC  
> Updated at: 2020-02-12 09:30:33 UTC  
> Url: https://github.com/boostorg/process/issues/121#issuecomment-585109834  

In my case I use a so called "single string interpreted as a command line"  (ref: [tutorial](https://www.boost.org/doc/libs/1_72_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.starting_a_process)) which comes from the outside and I expect that "that will cause the execution function to search the PATH variable to find the executable". So the explicit invocation of search_path seems to be a bit different option.  
  
I also need to notice that this worked great up to version 1.70.0.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2020-02-12 11:50:36 UTC  
> Url: https://github.com/boostorg/process/issues/121#issuecomment-585169593  

You're correct, that *should* work.

---

## Comment 4

> Username: alloutlife  
> Created at: 2020-02-12 14:40:50 UTC  
> Url: https://github.com/boostorg/process/issues/121#issuecomment-585236988  

But starting from version 1.71.0 it doesn't. The method ``executor::prepare_cmd_style()`` is introduced for the first time in this version.  
  
Just to be very clear I put a simple source code reproducing the issue (POSIX OS is assumed, I use Debian):  
  
  
```  
#include <string>  
#include <iostream>  
#include <boost/process.hpp>  
  
int main( void )  
{  
    ::putenv( const_cast< char* >( "PATH=/usr/bin:/bin" ) );                          // Triggers the issue  
    // ::putenv( const_cast< char* >( "PATH=/usr/local/bin:/usr/bin:/bin:" ) );       // Works fine  
  
    // Notice: wget is located in /usr/bin  
    std::string strCommanLine{ R"(wget -P /tmp/ https://dl.bintray.com/boostorg/release/1.72.0/source/boost_1_72_0.tar.gz.asc --no-verbose)" };  
  
    try {  
        boost::process::system( strCommanLine );  
        boost::process::system( "cat /tmp/boost_1_72_0.tar.gz.asc" );  
        return 0;  
    }  
    catch( std::exception& ex ) {  
        std::cout << ex.what() << std::endl;  
    }  
  
    return -1;  
}  
```  
  
Debugging the code figures out that ``executor::prepare_cmd_style()`` splits ``PATH`` into two elements of ``std::vector< std::string >`` : ``{ "PATH=/usr/bin", "/bin" }``.  
Then it builds the full path to the executable using all the paths discovered and checks if the resulting executable has exec permission.  
  
So with the first vector entry ``::access( "PATH=/usr/bin/wget", X_OK )`` gives false (although we know that wget is located exactly in /usr/bin). The second invocation of ``::access( "/bin/wget", X_OK )`` also gives false because there is no ``wget`` in ``/bin``.  
  
If we swap entries of the ``PATH`` variable (see the commented code in my example) everything works fine.
