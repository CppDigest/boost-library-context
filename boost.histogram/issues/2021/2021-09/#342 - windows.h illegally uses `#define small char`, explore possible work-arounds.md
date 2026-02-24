# #342 - windows.h illegally uses `#define small char`, explore possible work-arounds [Closed]

> Username: HDembinski  
> Created at: 2021-09-30 07:30:03 UTC  
> Updated at: 2021-09-30 09:20:57 UTC  
> Closed at: 2021-09-30 09:20:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/342  

According to my research, it is not possible to work around this error in the windows.h header in this library without breaking the API.  
  
https://stackoverflow.com/questions/21165891/is-small-a-keyword-in-c  
  
We could deprecate the old API for everyone and add new methods `large_part` and `small_part` as suggested in #341. When the preprocessor detects that `small` is set, it would not compile the deprecated API. This should fix the issue for those that are affected but not break the code of other people.
