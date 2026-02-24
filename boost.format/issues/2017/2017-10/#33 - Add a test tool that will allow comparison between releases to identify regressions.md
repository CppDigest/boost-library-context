# #33 - Add a test tool that will allow comparison between releases to identify regressions [Closed]

> Username: jeking3  
> Created at: 2017-10-18 15:00:05 UTC  
> Updated at: 2017-12-18 15:36:20 UTC  
> Closed at: 2017-10-22 21:04:22 UTC  
> Url: https://github.com/boostorg/format/issues/33  

In general, Boost.Format is a stable and long-lived component and used by many consumers.  Changes to the behavior of Boost.Format could cause widespread issues.  A mechanism is needed that will allow comparison of one edition of Boost.Format to another to ensure there haven't been any regressions.  
  
### Proposal ###  
  
Create a matrix tester that will attempt to generate every possible format syntax string expression, and test against a variety of inputs appropriate to that type, and output the results to a text file which will be considered the results file.  The results file format is:  
  
```  
# glibc.version = n  
{format specification}<tab>OK |ERR<tab>string result or exception message  
...  
```  
  
The tool will also allow for results to be generated against the platform's snprintf call, so that boost::format and snprintf on the same inputs and format specifications can be compared.
