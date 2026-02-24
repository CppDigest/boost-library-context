# #212 - `bp::punct` is not matching all the characters it should [Closed]

> Username: necessarily-equal  
> Created at: 2025-02-07 06:01:36 UTC  
> Updated at: 2025-02-16 19:17:02 UTC  
> Closed at: 2025-02-16 19:17:00 UTC  
> Url: https://github.com/boostorg/parser/issues/212  

There is a bug with `bp::punct`. Not all the characters that bp::punct should match, are correctly matched. Here's a demonstrating example:  
  
```C++  
int main() {  
        std::vector<const char32_t*> strings = {   
                U"(",  
                U")",  
                U"[",  
                U"]",  
                U"{",  
                U"}",  
                U"-",  
                U"—", // em-dash  
                U"᠃", // mongolian full stop  
                U"†", // dagger  
                U"‰", // per thousands  
                U"‱", // per ten thousands  
                U"‵", // reversed prime  
                U"‶", // reversed double prime  
                U"‷", // reversed triple prime  
                U"‸", // caret  
                U"※", // reference mark  
                U"‽", // interrobang  
                U"⁉️", // exclamation-question mark emoji  
                U"⁅", // left square bracket with quill  
                U"₍", // subscript left parenthesis  
                U"⸸", // upside-down dagger  
        };  
        for (auto str : strings) {  
                auto r = bp::parse(std::u32string(str), bp::punct);  
  
                if (r)   
                        std::cout << "match" << std::endl;  
                else  
                        std::cout << "don't match" << std::endl;  
        }  
        return 0;  
}  
```  
  
gives:  
```  
match  
match  
don't match  
don't match  
don't match  
don't match  
match  
match  
match  
match  
match  
match  
match  
match  
match  
match  
match  
match  
don't match  
don't match  
match  
don't match  
```  
  
But all of those should, as far as I understand, match. For example, [{](https://util.unicode.org/UnicodeJsps/character.jsp?a=007B) and [⸸](https://util.unicode.org/UnicodeJsps/character.jsp?a=2E38) are listed in the [reference list used](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3APc%3A%5D%5B%3APd%3A%5D%5B%3APe%3A%5D%5B%3APf%3A%5D%5B%3APi%3A%5D%5B%3APs%3A%5D%5B%3APo%3A%5D) and are explicitely listed in `struct char_set<punct_chars>` as `0x7B` and `0x2E38`. So they would be expected to be matched  
  
(I am not sure why this isn't working properly as I didn't dive in the code yet. I have a hunch that suggests it could be because of a faulty binary search algorithm.)  
  
Thanks!

---

## Comment 1

> Username: necessarily-equal  
> Created at: 2025-02-07 06:13:40 UTC  
> Url: https://github.com/boostorg/parser/issues/212#issuecomment-2642033497  

Figured it out: https://github.com/boostorg/parser/issues/209. Since the list was not sorted, it was indeed a binary search problem!  
  
Maybe the fix should be merged into the master branch?

---

## Comment 2

> Username: tzlaine  
> Created at: 2025-02-16 19:17:01 UTC  
> Url: https://github.com/boostorg/parser/issues/212#issuecomment-2661577145  

Yep, that was it, sadly.  I don't usually merge to master until It's near release time.  The develop branch is the default on when you do a clone -- that's the one to use for between-release fixes.
