# #64 - Inconsistent behavior - boost::locale defines `numpunct` for non ICU backends [Open]

> Username: salvoilmiosi  
> Created at: 2021-07-03 18:37:03 UTC  
> Updated at: 2024-11-11 21:54:02 UTC  
> Url: https://github.com/boostorg/locale/issues/64  

```  
#include <boost/locale.hpp>  
#include <iostream>  
int main() {  
  boost::generator gen;  
  auto &facet = std::use_facet<std::numpunct<char>>(gen("it_IT.UTF-8"));  
  std::cout << facet.thousand_separator() << ' ' << facet.decimal_point() << '\n';  
}  
````  
This code always outputs ", ." regardless of locale.  
Right now the only way to extract std::numpunct information is to format an arbitrary number to a string and extract its, say, second and sixth character, and that's not a very efficient implementation.  
I would suggest creating a std::numpunct subclass in icu/numeric.cpp that extracts that information from the `icu::DecimalFormatSymbols` class using `getSymbol`

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2021-07-03 19:27:49 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873458741  

Because `std::numpunct` is broken by design.   
  
I explain. It shouldn't ever return character but a string. For example in many locales thousands separator is NBSP character and it can't be represented as `char` in UTF-8. There are some other cases.  
  
Additionally `std::locale` never modifies default locale and keeps it C locale since it can easily break some application (writing to CSV for example) and if you want to format a localised number you tell it explicitly

---

## Comment 2

> Username: salvoilmiosi  
> Created at: 2021-07-03 19:42:19 UTC  
> Updated at: 2021-07-03 19:42:50 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873460402  

Also the posix backend does implement a subclass of std::numpunct and ignores all outputs that have a size>1, reverting decimal_point to dot and thousand sep to comma. Why can't it be implemented the same way in icu? I have quickly written a mockup and it works:  
```  
template<typename CharType>  
struct icu_numpunct : public std::numpunct<CharType> {  
    typedef std::basic_string<CharType> string_type;  
public:  
    icu_numpunct(icu::Locale const &loc)  
    {  
        boost::locale::impl_icu::icu_std_converter<CharType> cnv("UTF-8");  
        UErrorCode err;  
        icu::DecimalFormatSymbols syms(loc, err);  
        decimal_point_ = cnv.std(syms.getSymbol(icu::DecimalFormatSymbols::kDecimalSeparatorSymbol));  
        if (decimal_point_.size() > 1) {  
            decimal_point_ = CharType('.');  
        }  
  
        thousands_sep_ = cnv.std(syms.getSymbol(icu::DecimalFormatSymbols::kGroupingSeparatorSymbol));  
    }  
protected:  
    virtual CharType do_decimal_point() const  
    {  
        return *decimal_point_.c_str();  
    }  
    virtual CharType do_thousands_sep() const  
    {  
        return *thousands_sep_.c_str();  
    }  
  
private:  
    string_type decimal_point_;  
    string_type thousands_sep_;  
};  
```

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2021-07-03 20:35:25 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873466676  

Because in POSIX backend I tried to do the best I can given the tools (sine I didn't format the number on my own). If you want to use some ICU specific API just use ICU directly.

---

## Comment 4

> Username: Flamefire  
> Created at: 2021-07-04 10:33:59 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873562198  

@artyom-beilis I think it is still a good idea to implement this:  
  
> in POSIX backend I tried to do the best I can given the tools  
  
--> The above mockup shows that "given the tools" (ICU here) one can implement that with minimal effort in a "good enough" way.  
  
IMO simply returning ", ." is wrong, if that doesn't match what is actually used at all. Returning wrong results is often problematic, so I'd expect an exception or similar here instead if the API can't be fulfilled.  
Implementing the ICU numpunct with best effort to improve that does sound right to me and I'd be willing to review and ultimately accept a PR for that.

---

## Comment 5

> Username: artyom-beilis  
> Created at: 2021-07-04 10:42:08 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873563308  

> IMO simply returning ", ." is wrong  
  
It is right and I explain why.  
  
Lets look on the code:  
  
    std::cout << 1235.63 << " " << boost::locale::as::number << 1235.63  << std::endl  
  
Will print lets say in ru_RU.UTF-8 locale following:  
  
    1234.67 1.245,67  
  
Note: numpunct still refer to standard facet that is used for output. If you want to format localized number you use `boost::locale::as::number` that uses difference facet and it does not use `std::numpunct`

---

## Comment 6

> Username: salvoilmiosi  
> Created at: 2021-07-04 10:43:34 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873563515  

The problem is that some external libraries rely on `std::numpunct` for number formatting and parsing.

---

## Comment 7

> Username: artyom-beilis  
> Created at: 2021-07-04 10:46:57 UTC  
> Updated at: 2021-07-04 10:47:52 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873563986  

> The problem is that some external libraries rely on `std::numpunct` for number formatting and parsing.  
  
Then they do plain wrong since std localization is broken, at point that it actually generated wrong UTF-8 because shown only 1st byte of multi-byte sequence.  
  
I have no reason to fix something broken by design. If you want to format number it is also wrong to use facet because number formatting is much more than just separator for example ١٢ is very valid and good number... Are you supporting it as well?

---

## Comment 8

> Username: salvoilmiosi  
> Created at: 2021-07-04 10:54:29 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873564999  

Why is std::numpunct implemented in all localization backends except icu then? I get it you're trying to use whatever tools the language gives you, but this is inconsistent design. Why don't we just add a boost::locale::numpunct that returns std::string?

---

## Comment 9

> Username: salvoilmiosi  
> Created at: 2021-07-04 11:07:12 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873566976  

This is what I have to do to make decimal_for_cpp compatible with boost.locale, is this "broken by design"?  
```  
#include <boost/locale.hpp>  
  
namespace bl = boost::locale;  
  
int main(int argc, char**argv) {  
    const char *lang = "";  
    if (argc > 1) {  
        lang = argv[1];  
    }  
  
    std::locale loc = bl::generator{}(lang);  
    std::cout.imbue(loc);  
  
    std::cout << bl::as::number << 1000.5 << '\n';  
  
    auto custom_lbm = bl::localization_backend_manager::global();  
#ifdef _WIN32  
    custom_lbm.select("winapi");  
#else  
    custom_lbm.select("posix");  
#endif  
  
    auto numloc = bl::generator{custom_lbm}(lang);  
    auto &numfacet = std::use_facet<std::numpunct<char>>(numloc);  
    std::cout << "Thousand: " << numfacet.thousands_sep() << '\n';  
    std::cout << "Decimal: " << numfacet.decimal_point() << '\n';  
  
    return 0;  
}  
```

---

## Comment 10

> Username: Flamefire  
> Created at: 2021-07-04 11:19:25 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873569005  

> It is right and I explain why.  
  
I have to agree to @artyom-beilis here. Check this code:  
  
```  
#include <boost/locale.hpp>  
#include <iostream>  
  
int main() {  
  boost::locale::generator gen;  
  auto loc = gen("it_IT.UTF-8");  
  {  
    auto &facet = std::use_facet<std::numpunct<char>>(loc);  
    std::cout << facet.thousands_sep() << ' ' << facet.decimal_point() << '\n';  
    std::cout.imbue(loc);  
    std::cout << 1234.56 << '\n';  
//, .  
//1234.56  
  }  
  loc = std::locale("it_IT.UTF-8");  
  {  
    auto &facet = std::use_facet<std::numpunct<char>>(loc);  
    std::cout << facet.thousands_sep() << ' ' << facet.decimal_point() << '\n';  
    std::cout.imbue(loc);  
    std::cout << 1234.56 << '\n';  
//. ,  
//1.234,56  
  }  
```  
  
So from what I can tell this is fully correct. the sep and decimal_point are shown as used.  
  
`bl::as::number` may use something different but it doesn't claim to use `std::numpunct`, and if it did, then it would be a documentation bug.  
@artyom-beilis Is there a `std::numpunct` equivalent for `bl::as::number` ?

---

## Comment 11

> Username: artyom-beilis  
> Created at: 2021-07-04 11:22:30 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873569485  

> Why is std::numpunct implemented in all localization backends except icu then? I get it you're trying to use whatever tools the language gives you, but this is inconsistent design.  
  
This is good point.  
  
The issue that I shouldn't modify `std::numpunct` at all and this is I agree is inconsistency it should be C backend regardless of the locale by default.  
  
https://github.com/boostorg/locale/blob/develop/src/util/numeric.hpp#L119  
  
The special case should be all others and not `posix`. In any case just to show you an example of what happens if you try to use `std::locale`  
  
    #include <iostream>  
    #include <locale>  
    int main()  
    {  
        std::cout.imbue(std::locale(""));  
        std::cout << 1456.34567 << std::endl;  
    }  
  
The result is:  
  
    LANG=ru_RU.UTF-8 ./a.out   
    1�456,35

---

## Comment 12

> Username: Flamefire  
> Created at: 2021-07-04 11:28:08 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873570242  

Woops, yes: Check this:  
  
```  
  auto custom_lbm = boost::locale::localization_backend_manager::global();  
  custom_lbm.select("posix");  
  boost::locale::generator gen(custom_lbm);  
  auto loc = gen("it_IT.UTF-8");  
  {  
    auto &facet = std::use_facet<std::numpunct<char>>(loc);  
    std::cout << facet.thousands_sep() << ' ' << facet.decimal_point() << '\n';  
    std::cout.imbue(loc);  
    std::cout << 1234.56 << '\n';  
  }  
```  
It returns  
```  
. ,  
1234.56  
```  
And that IS wrong, isn't it? --> With ICU backend it is correct, with POSIX it is broken

---

## Comment 13

> Username: salvoilmiosi  
> Created at: 2021-07-04 11:30:35 UTC  
> Updated at: 2021-07-04 11:31:13 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873570559  

> `std::cout << 1234.56 << '\n';`  
  
you need to use boost::locale::as::number for that kind of formatting.

---

## Comment 14

> Username: Flamefire  
> Created at: 2021-07-04 11:32:31 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873570920  

> you need to use boost::locale::as::number for that kind of formatting.  
  
Not if I specifically want to use the std formatting. I.e. pretend to be some component which does not know about Boost.Locale

---

## Comment 15

> Username: artyom-beilis  
> Created at: 2021-07-04 11:33:51 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873571169  

Actually even for C locale thounthands separator is ","  but there is no grouping..  
  
The bug/inconsistency is that I can for example give decimal_separator = "," with de_DE locale on POSIX/WIN/STD backends while it should be "."

---

## Comment 16

> Username: artyom-beilis  
> Created at: 2021-07-04 11:50:32 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-873573727  

Just to warn the participants... I have no idea when I'll be able to fix it. Since it isn't that trivial.  
  
So I suggest to look for a workaround before and not wait to fix :-(

---

## Comment 17

> Username: Flamefire  
> Created at: 2023-06-28 16:13:29 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-1611720170  

> Since it isn't that trivial.  
  
While working at this I now understand why. And the inconsistencies bug me, not sure how to resolve them:  
  
1. The default formatting with a generated locale but without being Boost.Locale aware is inconsistent regarding `numpunct`, see #174  
2. Only the std backend has localized names for true/false  
3. You can use `as::number` or `as::posix` to switch between localized and classic formatting/parsing, but nothing exists for true/false localization  
4. In the documentation it is stated since forever that the default is non-localized formatting  
  
Making the `std::numpunct` facet return the "classic" values resolves 1. But now there is no good way to return localized values.  
  
One solution is to have a fully separate `boost::locale::numpunct` returning localized values as strings instead of chars avoiding the encoding issue **and** having a classic/posix-`std::numpunct`. This matches the use case described in https://github.com/boostorg/locale/issues/174#issuecomment-1564761741 / the documentation  
  
However it breaks existing "workarounds" such as the one described in https://github.com/boostorg/locale/issues/64#issuecomment-873566976 and makes it impossible to use a localized input/output in third-party libs.  
  
Moreover there is no existing solution when/how localized boolalpha input/output should be used.    
And in more general: There is currently no way to have third-party libraries (even Boost.LexicalCast) use localized input/output  
  
So regarding the doku example (writing a CSV file) and related: A library implicitly expecting classic-locale but not actually setting it may as well be considered broken.  
  
So idea:  
- an additional manipulator next to `as::posix`/`as::number`: `as::localized`  
- have a switch in the generator to enable localized input/output by default, i.e. use either `as::posix` or `as::localized` when nothing is specified for a stream  
- `as::number` will format bools as numbers (likely breaking change), while `as::localized` will use the current translated name and `as::posix` will use "true"/"false"  
- independent `boost::locale::numpunct` facet using strings  
- optional: when "localized" is the default, create "best-effort" std::numpunct facets (like current POSIX/WinAPI). Not sure if this is good as this will be "sometimes wrong" which might be worse than "always wrong". At least the non-char variants could be correct though, only the char variant will be affected by the wrongness. No good solution here.

---

## Comment 18

> Username: Flamefire  
> Created at: 2024-11-11 21:54:01 UTC  
> Url: https://github.com/boostorg/locale/issues/64#issuecomment-2469122308  

This is related to #235: `std::numpunct` is not defined for the ICU backend so using `std::num_put/num_get` with those locales will use the C locale. This happens when the ICU  `num_put/num_get` implementations uses the fallback which happens for:  
  
1. posix input/output  
2. input/output in non-decimal bases  
3. `uint64_t` output with numbers larger than `INT64_MAX`  
4. error when getting a formatter instance  
  
The 2nd case can be ignored for this.     
So while we do definitely want a C/Posix-locale `numpunct` for the ICU backend in the first case, we actually would want a custom `numpunct` for the fallback 3 & 4  
  
There could be a problem with parsing a number written before: The posix locale doesn't use a grouping, hence no thousand separator. If we try to parse a locale formatted number with the fallback it might fail if the locale used grouping.     
So only case 4 can be problematic: If we write using a grouping but have an error while parsing using the fallback C locale parser will fail the parsing.  
  
Case 3 is basically what happens in #235: Such numbers get formatted using the C locale although it might be possible to use locale specific formatting when the separators are single-byte. Note that the approach of using a replacement char doesn't work for the ICU backend as e.g. an `uint64_t` written with a replacement char cannot be parsed back by the ICU parser class which expects the locale specific separators.
