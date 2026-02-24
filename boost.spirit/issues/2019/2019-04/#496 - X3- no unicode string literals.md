# #496 - X3: no unicode string literals [Closed]

> Username: cmazakas  
> Created at: 2019-04-24 15:28:41 UTC  
> Updated at: 2019-04-26 22:12:24 UTC  
> Closed at: 2019-04-25 13:03:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/496  

The following code fails to compile:  
  
https://wandbox.org/permlink/sVVNLHWXWtvrhMKm  
  
Looking at the source code for `literal.hpp`, it seems like Unicode is nowhere to be found but `standard_wide` is.  
  
How does one work with UTF encoded string literals in X3?

---

## Comment 1

> Username: djowel  
> Created at: 2019-04-24 22:01:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/496#issuecomment-486444207  

Typically, use UTF8 and the UTF8 iterators in boost: http://tinyurl.com/y4z7xgme or some other means. That said, there's no reason not to support unicode. It's just that x3 predated these facilities. I'll work on it.

---

## Comment 2

> Username: djowel  
> Created at: 2019-04-25 13:01:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/496#issuecomment-486662542  

lit support for 32-bit unicode has been added (develop branch). Take note that X3 supports only full char32_t code points, hence the code changes:  
  
```c++  
#define BOOST_SPIRIT_X3_UNICODE  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/support/char_encoding/unicode.hpp>  
  
#include <boost/utility/string_view.hpp>  
  
#include <iostream>  
#include <utility>  
  
namespace x3 = boost::spirit::x3;  
  
using namespace boost::spirit::x3::unicode;  
  
int  
main()  
{  
   auto const input = boost::u32string_view(U"rawr");  
  
   auto pos = input.begin();  
   bool r = x3::parse(pos, input.end(), lit(U"rawr"));  
  
   return 0;  
}  
```  
  
Also, take note that there are more hidden unicode facilities in spirit (e.g. see https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/support/char_encoding/unicode.hpp). And there's a lot! These need to be aded to the char class parsers. Currently, only the basics are provided: https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/char/char_class.hpp  
  
There's no unicode for those yet. It would be great if you could contribute/add the missing pieces if you have time.  
  
See https://github.com/boostorg/spirit/issues/499

---

## Comment 3

> Username: cmazakas  
> Created at: 2019-04-25 16:18:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/496#issuecomment-486740640  

Actually, I'm not opposed to contributing.  
  
So, I inspected the source a bit more. I'm not sure what I'm missing that'd make:  
```  
BOOST_SPIRIT_X3_CHAR_CLASSES(unicode)  
```  
not Just Work.  
  
It can be a little tough to collaborate through github issues though. Do you happen to be on the cpplang slack? I've tried IRC but it's kind of a ghost town.

---

## Comment 4

> Username: djowel  
> Created at: 2019-04-26 03:09:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/496#issuecomment-486909857  

Well, examples, tests to make sure everything is good; ... and ahem ahem... docs. As you can see a lot of the heavy lifting has already been implemented. Also it would be nice to provide more utf8 (or even utf16) conversion examples. utf8 is still the most optimal as a front-end, which then converts to char32_t code points that spirit can process directly.  
  
There is a Spirit mailing list, actually, and also a Boost-devel mailing list. For now, we can probably just use github issues and PRs as it is right now.

---

## Comment 5

> Username: cmazakas  
> Created at: 2019-04-26 15:27:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/496#issuecomment-487098714  

Oh, docs! I can write those!  
  
And yeah, the test suite could be a bit more fleshed out. I can try helping with that too.  
  
In _that_ case, I have like a million questions then. But it'll probably be the most time-efficient if I write some examples with documentation and then have you review that and we can build from there.

---

## Comment 6

> Username: djowel  
> Created at: 2019-04-26 22:12:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/496#issuecomment-487217077  

Awesome! I'm glad I asked. Actually, beyond the char classes, there's a lot of docs that need to be written :-)
