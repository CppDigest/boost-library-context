# #228 - How to Verify boost::locale::generator? [Closed]

> Username: danielsafrigin  
> Created at: 2024-04-26 00:39:18 UTC  
> Updated at: 2025-01-12 16:37:07 UTC  
> Closed at: 2025-01-12 16:37:07 UTC  
> Url: https://github.com/boostorg/locale/issues/228  

I'm using Ubuntu with full boost installation with ICU.   
The following code will always result in "Locale Name: *".  
It doesn't matter if it's a valid locale like "en_US.UTF-8", or a not valid one like "super_random_text.UTF-8", name() always returns *.  
  
```  
#include <iostream>  
#include <boost/locale.hpp>  
  
int main() {  
    try {  
        boost::locale::generator gen;  
        std::locale loc = gen("en_US.UTF-8");  
        std::cout << "Locale Name: " << loc.name() << std::endl;  
    } catch (const std::exception& e) {  
        std::cerr << "Error creating locale: " << e.what() << std::endl;  
    }  
      
    return 0;  
}  
```  
  
I can validate for a Turkish locale by testing functionality:  
  
```  
#include <iostream>  
#include <string>  
#include <boost/locale.hpp>  
  
std::string to_lower(const std::string& text, const std::string& locale_name) {  
    boost::locale::generator gen;  
    std::locale loc = gen(locale_name);  
    return boost::locale::to_lower(text, loc);  
}  
  
void print_hex(const std::string& text) {  
    for (char c : text) {  
        std::cout << std::hex << static_cast<int>(static_cast<unsigned char>(c)) << " ";  
    }  
    std::cout << std::endl;  
}  
  
int main() {  
    // Uppercase text that we'll convert to lowercase  
    std::string english_text = "ISTANBUL";  
  
    // Convert to lowercase with the English locale  
    std::string english_lower = to_lower(english_text, "en_US.UTF-8");  
    std::cout << "English Lowercase: " << english_lower << std::endl;  
    print_hex(english_lower);  
  
    // Convert to lowercase with the Turkish locale  
    std::string turkish_lower = to_lower(english_text, "tr_TR.UTF-8");  
    std::cout << "Turkish Lowercase: " << turkish_lower << std::endl;  
    print_hex(turkish_lower);  
  
    return 0;  
}  
```  
  
output:  
  
```  
English Lowercase: istanbul  
69 73 74 61 6e 62 75 6c  
  
Turkish Lowercase: ıstanbul  
c4 b1 73 74 61 6e 62 75 6c  
```  
  
  
is there an appropriate way to test for any locale we generate if it's valid?

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-04-26 15:53:39 UTC  
> Url: https://github.com/boostorg/locale/issues/228#issuecomment-2079660686  

>  The following code will always result in "Locale Name: *".  
  
That is the expected behavior as defined by the C++ standard: Basically any modified locale has the name "*"  
  
> is there an appropriate way to test for any locale we generate if it's valid?  
  
It was a design decision (by the original author) that there is no such thing as an invalid locale. You can use Boost.Locale to generate a `std::locale` for any name you want. Any "part" that doesn't "exist" is basically using the C locale. This is true for all of the library: Try as good as possible and gradually fall back to the C locale to provide "something reasonable"  
  
Note that not constraining the input locale name to something that can be "fully supported" is useful to provide partial support for e.g. only formatting and/or translations.
