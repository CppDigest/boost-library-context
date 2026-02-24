# #72 - Potential issue: Stacktrace on Linux via addr2line won't work when process is looked up via PATH [Closed]

> Username: Schreischildkroete  
> Created at: 2019-01-30 12:11:42 UTC  
> Updated at: 2024-07-07 17:31:28 UTC  
> Closed at: 2024-07-07 17:31:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/72  

Not sure if this can be considered a design issue or it's working as intended. However, when a stack trace is printed the line number an/or symbols can only be resolved via addr2line, when the process is NOT resolved via PATH. Otherwise the symbols are not correctly resolved, because internally the process is not found via addr2line.   
  
If you need more information please feel free to ask.

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-05-04 18:24:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/72#issuecomment-489352215  

That's strange. addr2line based implementation uses `dladdr` for getting the symbol location. And that approach usually returns an absolute path via `dli_fname`.  
  
What OS are you using? Is it Android?

---

## Comment 2

> Username: j-jr-richter  
> Created at: 2020-07-23 09:10:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/72#issuecomment-662901036  

Problem exists also on SLE-15 (SUSE Linux Enterprise).  
  
Root cause is that for the main program `dli_fname` is not absolute andhence `addr2line` fails.  
  
This small patch fixes it for me:  
  
```  
--- /usr/include/boost/stacktrace/detail/addr2line_impls.hpp    2018-05-25 20:30:40.000000000 +0200  
+++ boost/stacktrace/detail/addr2line_impls.hpp 2020-07-22 10:43:25.242050818 +0200  
@@ -114,7 +114,7 @@  
     std::string res;  
   
     boost::stacktrace::detail::location_from_symbol loc(addr);  
-    if (!loc.empty()) {  
+    if (!loc.empty() && strchr(loc.name(), '/') != NULL) { // for programs started through $PATH loc.name() is not absolute and addr2line will fail  
         res = loc.name();  
     } else {  
         res.resize(16);  
```
