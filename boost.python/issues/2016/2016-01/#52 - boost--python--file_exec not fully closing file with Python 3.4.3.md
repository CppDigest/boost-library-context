# #52 - boost::python::file_exec not fully closing file with Python 3.4.3 [Closed]

> Username: asies  
> Created at: 2016-01-08 09:35:58 UTC  
> Updated at: 2016-09-01 04:10:58 UTC  
> Closed at: 2016-09-01 04:10:58 UTC  
> Url: https://github.com/boostorg/python/issues/52  

Using Python 3.4.3 and Boost 1.57.0 on 64-bit Windows 7 with Visual Studio 2013.  
  
After boost::python::exec_file is called on a file, that file can't be modified by the program using fopen_s, I think because it is still open.  
  
In the following example, when ModifyFile is called before executing the file, it succeeds. After the file has been executed, fopen_s returns 13 (Permission denied) and ModifyFile fails.  
  
``` python  
#include "stdafx.h"  
#include "boost\python.hpp"  
#include <iostream>  
  
#define FILE_NAME         "myTest.py"  
  
void ModifyFile( std::string newContent )  
{  
  FILE* myFile = nullptr;  
  errno_t result = fopen_s( &myFile, FILE_NAME, "wb" );  
  if ( result == 0 )  
  {  
    fwrite( newContent.c_str( ), sizeof( byte ), newContent.length( ), myFile );  
    fclose( myFile );  
    std::cout << "Success" << std::endl;  
    return;  
  }  
  
  std::cout << "Failure" << std::endl;  
}  
  
int main( int argc, char** argv )  
{  
  Py_Initialize( );  
  
  ModifyFile( "print(\"Hello\")" );  
  
  boost::python::api::object mainNamespace = boost::python::import( "__main__" ).attr( "__dict__" );  
  boost::python::exec_file( FILE_NAME, mainNamespace, mainNamespace );  
  
  ModifyFile("print(\"Goodbye\")");  
  
  Py_Finalize( );  
  return 0;  
}  
```  
  
I have tried a similar example using std::fstream to modify the file, and this doesn't seem to have the same problems.

---

## Comment 1

> Username: hotgloupi  
> Created at: 2016-01-08 10:43:17 UTC  
> Url: https://github.com/boostorg/python/issues/52#issuecomment-169960779  

It might be that std::fstream uses [_fsopen()](https://msdn.microsoft.com/en-us/library/8f30b0db.aspx) internally. In any case I would expect that exec_file() releases the file handle.

---

## Comment 2

> Username: asies  
> Created at: 2016-01-08 10:51:49 UTC  
> Url: https://github.com/boostorg/python/issues/52#issuecomment-169962588  

The extra b tells the file to be opened in binary mode (https://msdn.microsoft.com/en-us/library/z5hh6ee9.aspx).  
In any case, I think I also tested the example with the following changes and got the same behaviour:  
- opening the file with just "w" instead of "wb"  
- writing with sizeof(char) instead of sizeof(byte)  
  
So hopefully that detail isn't critical to the example.  
  
I've got my main project working by using Python's C API to execute the file (https://docs.python.org/3.5/c-api/veryhigh.html?highlight=pyrun_#c.PyRun_FileEx). The API includes parameters that specifically refer to the file being closed after execution, which is partly why I'm suspicious of boost::python::exec_file in the example above.

---

## Comment 3

> Username: hotgloupi  
> Created at: 2016-01-08 10:58:33 UTC  
> Url: https://github.com/boostorg/python/issues/52#issuecomment-169963731  

Yes, you're right. I think the bug is indeed in `exec_file()`. There's a 3 line patch in #53, please let me know if that fixes your issue.

---

## Comment 4

> Username: asies  
> Created at: 2016-01-08 12:03:38 UTC  
> Url: https://github.com/boostorg/python/issues/52#issuecomment-169981021  

Thank you, I will do. I'm not sure how long it will take, so it might be Monday before I can get back you you.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2016-01-08 12:31:29 UTC  
> Url: https://github.com/boostorg/python/issues/52#issuecomment-169985779  

There was a related issue reported in the past that has been addressed in Boost.Python 1.59 or earlier. Could you try a newer version (like the current Boost 1.60) to make sure you are not seeing a bug that's already ben fixed ?

---

## Comment 6

> Username: asies  
> Created at: 2016-01-08 13:30:00 UTC  
> Url: https://github.com/boostorg/python/issues/52#issuecomment-170004358  

Unfortunately I can't currently try a newer version of Boost, sorry. I agree that it would be preferable to test the example with 1.60 if possible though.  
  
Copying the change from #53 into Boost 1.57 has fixed the problem in the example, so provisionally it looks good.

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2016-09-01 04:10:58 UTC  
> Url: https://github.com/boostorg/python/issues/52#issuecomment-243971195  

Sounds like this is fixed now.
