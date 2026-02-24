# #198 - Windows handle leak when process::child fails to execute a file [Open]

> Username: inkmeister  
> Created at: 2021-01-29 15:58:17 UTC  
> Updated at: 2021-01-29 16:04:01 UTC  
> Url: https://github.com/boostorg/process/issues/198  



---

## Comment 1

> Username: inkmeister  
> Created at: 2021-01-29 16:02:59 UTC  
> Url: https://github.com/boostorg/process/issues/198#issuecomment-769892855  

Hi,  
  
We have traced a handle leak in our Windows application down to boost::process::child which appears to leak when it fails to execute a file. The following program demonstrates the leaking of two handles:  
  
```  
#include <boost\process.hpp>  
#include <boost\process\windows.hpp>  
  
	// This code will leak two handles when run  
	boost::process::opstream in;  
	boost::process::ipstream out;  
	try  
	{  
		boost::process::child c;  
		c = boost::process::child("c:/notafolder/notafile.exe", boost::process::windows::hide, boost::process::std_out > out, boost::process::std_in < in);  
	}  
	catch (const std::exception &)  
	{  
	}  
```  
  
Boost 1.74 and 1.75 appear to be affected. We have not tried earlier versions. This is on Windows 10 64-bit, compiling with Visual Studio 2019 (16.8.4).  
  
Cheers,  
Mark.
