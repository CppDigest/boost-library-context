# #98 - Improve robustness of INI parser by refusing NULL characters [Open]

> Username: bubnikv  
> Created at: 2022-05-04 07:02:10 UTC  
> Updated at: 2023-11-17 17:11:10 UTC  
> Url: https://github.com/boostorg/property_tree/issues/98  

We are facing a weird issue on Windows, where a file move operation is not atomic: It seems to copy the file and sometimes it produces files that start normally, but then end filled with NULL characters. It looks as if a buffer was prepared, possibly memory mapped and then never filled.  
  
Unfortunately the property_tree ini parser parses such files in many cases. Would it be possible to throw an exception on NULL characters, possibly as an option? Other characters below ASCII 32 excluding CR, LF, TAB may possibly be refused as well.

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-17 17:11:09 UTC  
> Url: https://github.com/boostorg/property_tree/issues/98#issuecomment-1816794102  

@bubnikv This appears to be an issue in your specific use-case. Changing the library's behavior to fix it might not be the right choice. Have you tried reading the file, applying the necessary filtration, and then passing an std::string to the parser?
