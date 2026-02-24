# #229 - [wandbox] File path on Windows [Closed]

> Username: badair  
> Created at: 2015-12-26 13:33:20 UTC  
> Updated at: 2015-12-26 19:48:39 UTC  
> Closed at: 2015-12-26 19:48:39 UTC  
> Url: https://github.com/boostorg/hana/issues/229  

The wandbox.py script doesn't work quite right on Windows. Since the script may never run on Windows for Hana's purposes, it may be a non-issue, but I thought it might be worth a mention.  
  
The issue can be fixed by changing line 102 of hana/cmake/wandbox.py to the following:  
  
'file': os.path.relpath(header, directory).replace('\','/')
