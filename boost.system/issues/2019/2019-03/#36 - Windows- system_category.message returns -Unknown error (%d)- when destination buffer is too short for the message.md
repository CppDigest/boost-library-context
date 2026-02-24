# #36 - Windows: system_category.message returns "Unknown error (%d)" when destination buffer is too short for the message [Closed]

> Username: Kojoley  
> Created at: 2019-03-15 22:46:58 UTC  
> Updated at: 2019-03-16 18:22:39 UTC  
> Closed at: 2019-03-16 18:22:39 UTC  
> Url: https://github.com/boostorg/system/issues/36  

The docs says:  
> copy a string describing the error into `buffer`, truncating it to `len-1` characters and storing a null terminator, and return `buffer`.  
  
Currently for any `FormatMessage` error "Unknown error (%d)" is returned.  
  
I cannot find a creditable source, but I see that `FormatMessage` fills the buffer and signaling `ERROR_INSUFFICIENT_BUFFER` via `GetLastError()`.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-03-15 23:04:09 UTC  
> Url: https://github.com/boostorg/system/issues/36#issuecomment-473467718  

It would be nice if we could rely on this, yes, but it's not documented anywhere.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-03-16 17:58:33 UTC  
> Url: https://github.com/boostorg/system/issues/36#issuecomment-473570279  

I tried that (on Windows 7), and when the buffer is too small, FormatMessage leaves it unchanged. The behavior may have changed on 8/8.1/10.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-03-16 18:22:39 UTC  
> Url: https://github.com/boostorg/system/issues/36#issuecomment-473572235  

Sorry for confusion. I did not recheck, spoted a filled buffer at debugging `system_category_test`, most likely I seen an error message from a previous successful invocation. Now made a clean test and it is not filling :(  
  
<details><summary>Test code</summary>  
  
```cpp  
#include <windows.h>  
#include <iostream>  
  
int main()  
{  
    char buffer[128];  
    std::memset(&buffer, 0, sizeof(buffer));  
  
    DWORD retval = FormatMessageA(  
        FORMAT_MESSAGE_FROM_SYSTEM | FORMAT_MESSAGE_IGNORE_INSERTS,  
        NULL,  
        5810,  
        MAKELANGID(LANG_NEUTRAL, SUBLANG_DEFAULT), // Default language  
        buffer,  
        sizeof(buffer),  
        NULL  
    );  
    DWORD errc = GetLastError();  
  
    std::cout << "retval: " << retval << " (should be 0)\n";  
    std::cout << "errc: " << errc << ((errc == ERROR_INSUFFICIENT_BUFFER) ? " ERROR_INSUFFICIENT_BUFFER" : "") << '\n';  
  
    int count = 0;  
    for (char c : buffer)  
        if (c)  
            ++c;  
    std::cout << count << " not null chars\n";  
}  
```  
  
</details>
