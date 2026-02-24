# #113 - valid json reported as invalid [Closed]

> Username: bhendrickson  
> Created at: 2020-06-19 08:29:36 UTC  
> Updated at: 2020-06-30 19:54:04 UTC  
> Closed at: 2020-06-30 19:54:04 UTC  
> Url: https://github.com/boostorg/json/issues/113  

The file below contains a valid json string. However, the boost json example program `validate` reports it is invalid, and the example program `pretty` reports `caught exception: extra data`.   
  
[valid_json_boost_json_issue.txt](https://github.com/CPPAlliance/json/files/4803279/valid_json_boost_json_issue.txt)  
  
Many small changes to the string makes it parse correctly, such as deleting an innocuous looking character from the string.  
  
I've reproduced this on the tip of master (f47fbeb1a54b832221a35c17912febb7a2c3a317) with both clang and gcc.  
  
Please let me know any additional information I can provide that would be helpful.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-06-21 02:31:13 UTC  
> Url: https://github.com/boostorg/json/issues/113#issuecomment-647069713  

@bhendrickson It seems like we were forgetting to flush the buffer after parsing escape sequences, causing the parse to be incomplete.
