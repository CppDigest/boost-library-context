# #61 - Test case fails on cygwin - lack of wide path support [Open]

> Username: jeking3  
> Created at: 2018-07-18 21:12:20 UTC  
> Updated at: 2018-08-19 14:33:45 UTC  
> Url: https://github.com/boostorg/iostreams/issues/61  

On cygwin, boost::filesystem uses wide character paths.  
The mapped_file implementation in iostreams only supports this on Windows proper.  
In cygwin it falls back to open() and there is no conversion code from the wide string that allows it to work.  Since the built-in path object supports wide and not wide, mapped_file currently cannot file any file if the path is wide because the path string is empty.  
  
https://github.com/boostorg/iostreams/blob/develop/src/mapped_file.cpp#L264  
  
I disabled one of the mapped file tests only for CYGWIN to work around this.  I was going to hook that code into boost::locale to leverage from_utf, but that creates a dependency that isn't currently there so I decided to open an issue for it instead.

---

## Comment 1

> Username: eldiener  
> Created at: 2018-08-16 01:44:48 UTC  
> Url: https://github.com/boostorg/iostreams/issues/61#issuecomment-413395835  

AFAIK Windows supports both 8-bit character paths as ascii or multibyte characters and 16-bit character paths in the form of UTF16, while Linux supports 8-bit character paths in the form of UTF8. Why would boost::filesystem only support 16-bit character paths for cygwin ?

---

## Comment 2

> Username: jeking3  
> Created at: 2018-08-19 14:33:45 UTC  
> Url: https://github.com/boostorg/iostreams/issues/61#issuecomment-414131777  

Look at https://github.com/boostorg/iostreams/blob/develop/src/mapped_file.cpp#L267 - the code in iostreams is calling ::open directly, so if the path is wide coming into the code here, there's no way for it to succeed.
