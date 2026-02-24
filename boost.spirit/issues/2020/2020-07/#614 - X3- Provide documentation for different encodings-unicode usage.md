# #614 - X3: Provide documentation for different encodings/unicode usage [Open]

> Username: NomAnor  
> Created at: 2020-07-04 16:24:05 UTC  
> Updated at: 2025-05-10 00:35:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/614  

Hi,  
I'm a new X3 user and I want to parse UTF-8 encoded souce code. The documentation has no explanation to do that (or rather no mention if there is something you have to look out for?).  
  
I'm confused by the source code. There are the namespaces standard, standard_wide, ascii, iso8859_1 and unicode. What exactly do these mean? Some are named after encodings, then there is standard and unicode seems to mean UTF-32.  
  
I would guess that as long as I use UTF-8 encoded input, my char type is `char` and I can use `std::string`. And for specifying literal parsers I should use `x3::lit(u8"")` to get the right utf-8 byte sequences. `x3::char_(u8'')` should work too as long as the character has a single byte representation.  
  
The documentation states that the character class parsers   
  
> Matches a character based on the equivalent of std::isalnum in the current character set   
  
How does that work with different encodings? Can I specify the locale myself instead of using the default one?  
  
Can you add a documentation page explaining all this?

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-07-04 19:13:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-653801356  

Spirit will not decode your UTF-8 input. I cannot say how the things are working, moreover, from my point of view, currently Spirit allows to do a lot of wrong things, but using character parsers from any namespace with `u8` literal is wrong, and should not compile in my opinion.  
  
What should work: decode your data into UCS-4 (it can be done on the fly via decoding iterators), and use character parsers from `x3::unicode` namespace (like `x3::unicode::char_(0xABCDE)`, 'U' literals seems to be not working #573).  
  
---  
  
> unicode seems to mean UTF-32  
  
Unicode is a character set, and UTF-32 is an character encoding.  
  
> The documentation states that the character class parsers  
>   
> > Matches a character based on the equivalent of std::isalnum in the current character set  
>   
> How does that work with different encodings?  
  
Character set and character encoding are different things.  
  
Unfortunately, they are messed in Spirit too, what creates more confusion.

---

## Comment 2

> Username: NomAnor  
> Created at: 2020-07-05 17:37:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-653916826  

I didn't use some terminology correctly. I couldn't resist to find out myself so I took a deep dive into C++ strings. I'm still alive and this is what I found out. Most is based on https://en.cppreference.com/w/ so not exactly the standard but hopefully close enough.  
  
### C++  
  
A **Character set** is a list of characters grouped together by someone. What exactly counts as a character is not always intuitive (combining characters for example). Examples: ASCII, ISO8859-X, Unicode.  
  
A **code point** is a number given to every character so we can use them with computers.  
  
An **encoding** is a way to store the code points. The basic blocks of storage are called **code units**. A character set can have multiple encodings but older character sets often have only one encoding so its not always clear if one talks about the character set or the encoding.  
  
The standard library deals with three different strings:  
  
- **null-terminated byte strings**. These use a one to one correspondence from code points to code units. Examples:  
  - ASCII (ASCII) uses one byte (but only 7 bit) per code point (`char`)  
  - ISO8859-X (ISO8859-X) uses one byte per code point (`char`). The first 128 characters encoded with ISO8859-X are the same as the ASCII encoding.  
  - UCS-2 (Unicode) uses two bytes per code point (don't know if the standard library has a type for this, would be `uint16_t`). This can not encode all possible Unicode code points.  
  - UTF-32/UCS-4 (Unicode) uses four bytes per code point (`char32_t`)  
  
- **null-terminated multibyte strings**. These use a variable amount of code units to store one code point. Examples:  
  - UTF-16 (Unicode) uses 16bit code units (`char16_t`) and one or two code units per code point.  
  - UTF-8 (Unicode) uses 8bit code units (`char`, since C++20 `char8_t`) and one to four code units per code point. The first 128 unicode characters encoded with UTF-8 are the same as the ASCII encoding.  
  
- **null-terminated wide strings**. These use code units (`wchar_t`) with a platform defined size (size depends on the os). I think these should not be multibyte but I'm not sure. These should not be used except when interfacing with os APIs that require them.  
  
The **locale** is used to encapsulate all cultural differences (number formating, time formatting, character categories, etc.) and the encoding to use (this is important for character classification). Character classes (e.g. `std::isalnum`) use the global locale or the specified one. The default global locale is the C locale using ASCII with ASCII encoding.  
The character classification only works with code points and so can only be used with null-terminated byte strings.  
  
### Spirit usage  
  
- The `standard` namespace contains parsers that will use the standard library functions (e.g. `std::isalnum`) and `char`. This means these can only be used with null-terminated byte strings using one byte per code point. Additionally the global locale must be set to one which uses the same encoding. Examples: ASCII (setting locale to `C`), ISO8859-1 (setting locale to `en_US.ISO-8859-1`)  
  
- The `standard_wide` namespace is the same but uses `wchar_t`. I'm not sure what the contraints are.  
  
- The `ascii` namespace contains parsers that will only work with ASCII encoded null-terminated byte strings using `char`. No locale is used.  
  
- The `iso8859_1` namespace contains parsers that will only work with ISO8859-1 encoded null-terminated byte strings using `char`. No locale is used.  
  
- The `unicode` namespace contains parsers that will only work with UTF-32 encoded null-terminated byte strings using `char32_t`. No locale is used.  
  
I would use these rules as guidelines on how to use Spirit correctly. If I'm wrong please correct me:  
  
- All character parsers will only work with null-terminated byte strings and the correct locale set (for `standard`). If one wants to parse for example UTF-8 input a conversion to UTF-32 must be made (I think there is an iterator adapter in boost or use `std::codecvt<char32_t, char, std::mbstate_t>` from the standard library).  
- Literal string parsers will also only work with null-terminated byte strings (in general they can use for example case insensitive comparing which requires character classification). I guess literal string parser that use case sensitive comparing might work with null-terminated multibyte strings.  
- Integer parsers only work with ASCII encoded null-terminated byte strings (they use `char_encoding::ascii` and the order of digits). Technically they would work with compatible encodings (ISO8859-X and UTF8).  
- Float parsers probably the same as Integer.  
- Bool parsers are the same as character parsers (case insensitive comparing)  
  
TLDR; Only use null-terminated byte strings and the ISO8859-1 encoding in conjuction with the `iso8859_1` namespace if everything should work out of the box.  
  
### Spirit issues  
The non-wide namespaces contain overloads for `wchar_t` for example  
  
https://github.com/boostorg/spirit/blob/53664b38e3d57c715b31a3aaf19bee6c8957825d/include/boost/spirit/home/x3/char/char.hpp#L66-L70  
  
Can somebody explain why? An ASCII encoded `wchar_t` doesn't make sense for me.  
  
How to implement the numeric parsers correctly to work with bigger encodings (`char32_t`)? I didn't test them in this setup. Will do if I find the time.  
  
Are there other parser that might be problematic?  
  
The `standard` namepace seems to be the default. The restrictions on `char` null-terminated byte strings and the necessity to set the correct global locale in this setup should definitly be documented. Because UTF-8 is compatible with ASCII means everything seems to work until you have non-ASCII characters in your input.  
  
If my findings are correct I'm willing to put this post in a new documentation page and make a pull request.

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-07-05 22:27:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-653946740  

> All character parsers will only work with null-terminated byte strings  
  
I am not sure I understand you. There is no such limitation. There is actually an opposite issue/missing feature -- no optimized handling of zero-terminated strings.  
  
> Integer parsers only work with ASCII  
> Float parsers probably the same as Integer.  
> Bool parsers are the same as character parsers (case insensitive comparing)  
  
Happily most (if not every) charsets are built as ASCII extension and are subsets of ASCII.  
  
> The non-wide namespaces contain overloads for `wchar_t` for example  
  
Yes, and it bothers me.  
  
> Can somebody explain why? An ASCII encoded `wchar_t` doesn't make sense for me.  
  
For me to, and I also has not received answer why it was done so. It is on my todo list for a long time.  
  
> The `standard` namepace seems to be the default  
  
This is a thing I do not like either. The most convenient solution (and because of the issue above) is to guess a proper parser.  
  
> Because UTF-8 is compatible with ASCII means everything seems to work until you have non-ASCII characters in your input.  
  
Yup, that's why I think it should fail at compile time, but it is only possible on distinct `char8_t`.

---

## Comment 4

> Username: djowel  
> Created at: 2020-07-05 22:42:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-653948053  

> Spirit will not decode your UTF-8 input. I cannot say how the things are working, moreover, from my point of view, currently Spirit allows to do a lot of wrong things, but using character parsers from any namespace with `u8` literal is wrong, and should not compile in my opinion.  
  
Agreed. These are the things that should be tightened.   
  
> What should work: decode your data into UCS-4 (it can be done on the fly via decoding iterators), and use character parsers from `x3::unicode` namespace (like `x3::unicode::char_(0xABCDE)`, 'U' literals seems to be not working #573).  
  
Yes, this is how x3::unicode is supposed to be used.

---

## Comment 5

> Username: djowel  
> Created at: 2020-07-05 22:45:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-653948322  

> Happily most (if not every) charsets are built as ASCII extension and are subsets of ASCII.  
>   
> > The non-wide namespaces contain overloads for `wchar_t` for example  
>   
> Yes, and it bothers me.  
>   
> > Can somebody explain why? An ASCII encoded `wchar_t` doesn't make sense for me.  
>   
> For me to, and I also has not received answer why it was done so. It is on my todo list for a long time.  
  
To be honest, I do not recall anymore. @Kojoley, do you know if it is something inherited from Qi? Or is this just an X3 quirk?

---

## Comment 6

> Username: djowel  
> Created at: 2020-07-05 22:47:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-653948473  

> > The `standard` namepace seems to be the default  
>   
> This is a thing I do not like either. The most convenient solution (and because of the issue above) is to guess a proper parser.  
  
What do you think should be default? Sorry, I am probably not parsing your statement correctly.

---

## Comment 7

> Username: Kojoley  
> Created at: 2020-07-05 23:42:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-653954258  

> To be honest, I do not recall anymore. @Kojoley, do you know if it is something inherited from Qi? Or is this just an X3 quirk?  
  
Inherited from Qi.  
  
> What do you think should be default? Sorry, I am probably not parsing your statement correctly.  
  
`x3::char_(L'a') == x3::standard_wide::char_(L'a')` and `x3::char_(U'a') == x3::unicode::char_(U'a')`. `u'a'` should also go into `x3::unicode`, I think.

---

## Comment 8

> Username: djowel  
> Created at: 2020-07-06 01:34:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-653972250  

> > To be honest, I do not recall anymore. @Kojoley, do you know if it is something inherited from Qi? Or is this just an X3 quirk?  
>   
> Inherited from Qi.  
>   
> > What do you think should be default? Sorry, I am probably not parsing your statement correctly.  
>   
> `x3::char_(L'a') == x3::standard_wide::char_(L'a')` and `x3::char_(U'a') == x3::unicode::char_(U'a')`. `u'a'` should also go into `x3::unicode`, I think.  
  
That makes a lot of sense. Let's go for it! Also, let's break away from Qi and do the right thing.

---

## Comment 9

> Username: NomAnor  
> Created at: 2020-07-11 17:27:39 UTC  
> Updated at: 2020-07-11 17:58:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/614#issuecomment-657099574  

I did some tests and this is a list of things I would change.  Some were already mentioned.  
  
### Character classifications not working with ISO8859-1  
Normally ``char`` is used for ISO8859-1 which can be signed resulting in negative code units. The ``char_class`` uses the ``Encoding::char_type`` which is ``char`` and not ``Encoding::classify_type`` which is ``unsigned char``. It also does not always convert the template ``Char`` type to this type.  
  
https://github.com/boostorg/spirit/blob/53664b38e3d57c715b31a3aaf19bee6c8957825d/include/boost/spirit/home/x3/char/char_class.hpp#L59-L69  
  
Here the ``encoding::ischar()`` does no conversion and only works because  
  
https://github.com/boostorg/spirit/blob/53664b38e3d57c715b31a3aaf19bee6c8957825d/include/boost/spirit/home/support/char_encoding/iso8859_1.hpp#L580-L586  
  
is specifically coded to work with negative values. There should be a cast to ``Encoding::classify_type`` as is used in  
  
https://github.com/boostorg/spirit/blob/53664b38e3d57c715b31a3aaf19bee6c8957825d/include/boost/spirit/home/x3/support/no_case.hpp#L43-L51  
  
https://github.com/boostorg/spirit/blob/53664b38e3d57c715b31a3aaf19bee6c8957825d/include/boost/spirit/home/x3/char/char_class.hpp#L23-L33  
  
Here an explicit cast is used but the target type is not ``classify_type``. The ``char_cast`` seems to be there so the signed types are handled correctly but again, the target type is the same as the source type.  
  
I'm not sure if the ``char_cast`` is necesarry (because it is not used in the case compare classes) or if a simple cast to the target type is enough. I think some unification for all casts is necessary.  
  
### standard namespace works with ``char32_t``  
The ``std::is*`` functions used by the standard namespace will only work correctly with ``char``. But because of the above casts and integer promotion (``char32_t`` -> ``int``, all ``Encoding::is*`` functions use ``int`` instead of ``classify_type``) using ``x3::standard::alpha`` with an ``std::u32string`` compiles.  
  
Using the standard namespace with anything else than ``char`` should give a compile error.  
  
The ``standard_wide``, ``ascii``, ``iso8859_1`` and ``unicode`` namespaces have the same problem. They should give comile errors if their ``char_type`` is not ``wchar_t``/``char``/``char32_t``. One could argue that the unicode namespace could work with ``char`` because of compatibility with ASCII/Latin-1 for those bytes but I think the user should be explicit about this and do his own cast.  
  
I'm not sure where the best place is to put a ``static_assert`` for this.  
  
### Number parsers work correctly  
What I said earlier is not correct. They work as long as the encoding used is compatible with ASCII. They compare the template ``Char`` type with literal characters with does integer promotion and will work correctly.   
Would it be considered 'best practice' to replace the character literals with u8 character literals so the encoding is guaranteed by the standard?  
  
### bool parser have no unicode namespace  
I don't see why it is missing, should be simple to add.  
  
Maybe only char and string parsers need the namespaces and all other parser are considered ASCII compatible like the number parsers.  
  
### ``ascii::lit()`` works with ISO8859-1 characters  
The ``literal_char`` class uses the case compare classes. These just compare the values but the set of ASCII characters is smaller than the set of ``char`` values. I think there should be an ``ischar()`` test.  
  
https://github.com/boostorg/spirit/blob/53664b38e3d57c715b31a3aaf19bee6c8957825d/include/boost/spirit/home/x3/support/no_case.hpp#L27-L31
