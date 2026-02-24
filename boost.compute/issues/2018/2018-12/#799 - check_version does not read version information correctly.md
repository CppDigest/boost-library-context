# #799 - check_version does not read version information correctly [Open]

> Username: karthickg8  
> Created at: 2018-12-04 21:26:02 UTC  
> Updated at: 2018-12-05 09:17:10 UTC  
> Url: https://github.com/boostorg/compute/issues/799  

Hello,  
  
check_version does not read version information correctly from the string. This is due to the missing "C" locale setting for the stringstream object. For some reason this is not a problem in Visual studio 2012 but it does not work in Visual studio 2017.  
  
Steps to reproduce:  
1. Keep Windows system locale to English (United States).  
2. **Change decimal separator to comma (',') and digital grouping to dot (.).**  
  
Then my application crashes on exit (crash stack trace attached).  
  
When I tried to step into code then I found, check_version function was not correctly streaming out the version numbers.  
I made a fix in my copy by adding following two lines to the check_version function right after 'stream << version();'  
  
		// Read version from stream using "C" locale and not user locale.  
		std::locale stdLocale("C");  
		stream.imbue(stdLocale);  
		  
This solved crash in our application.  
Could you please review my changes and add this fix or make a new fix to the boost libraries?  
  
This can also be tested in a test application. Please compile and run the attached TestApp.txt (rename extension to .cpp) program attached (bug reproducible in Visual studio 2017). Please make sure you follow the steps mentioned above (OS locale settings).  Without my fix, stream >> actual_major; actual_major has the value as 0 instead of 1.  
  
Thanks & regards,  
Karthick  
  
[crash_stack_trace.txt](https://github.com/boostorg/compute/files/2646146/crash_stack_trace.txt)  
[TestApp.txt](https://github.com/boostorg/compute/files/2646179/TestApp.txt)
