# #175 - boost::u32regex_replace() behavior when the string has a closing newline [Open]

> Username: msi22  
> Created at: 2022-05-18 09:43:07 UTC  
> Updated at: 2022-05-18 09:43:07 UTC  
> Url: https://github.com/boostorg/regex/issues/175  

It seems that boost::u32regex_replace() matches a newline with ^(.*)$ different when it is at the end of a string and not within the string.  
  
Input: "line\nline\nline\n"  
Regular expression: "^(.*)$"  
Format: "Hallo\\1"  
Result: "Halloline\nline\nline\nHallo  
  
Is this example intended to result in two matches and substitutions?  
  
[code.txt](https://github.com/boostorg/regex/files/8715090/code.txt)  
[output.txt](https://github.com/boostorg/regex/files/8715092/output.txt)
