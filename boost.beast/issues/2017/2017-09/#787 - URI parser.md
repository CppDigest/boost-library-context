# #787 - [Feature Request] URI parser [Closed]

> Username: anonimal  
> Created at: 2017-09-20 22:45:43 UTC  
> Updated at: 2022-12-14 16:55:59 UTC  
> Closed at: 2022-06-16 16:30:55 UTC  
> Url: https://github.com/boostorg/beast/issues/787  

Hi @vinniefalco et al.  
  
Are there plans to implement a URI parser similar to cpp-netlib?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-20 22:53:00 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-331003227  

Yes.

---

## Comment 2

> Username: anonimal  
> Created at: 2017-09-20 23:00:41 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-331004547  

@vinniefalco fantastic! 👍 Can we rename this issue as a feature request?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-20 23:45:00 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-331011226  

Sure, although it would probably appear as a piece of example code at first. When it becomes a public interface, it will likely be part of a different library (which will also be submitted to Boost).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-09-20 23:56:11 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-331012847  

You can see some of the partial results here  
https://github.com/vinniefalco/beast/commits/uri

---

## Comment 5

> Username: theopolis  
> Created at: 2017-11-08 04:16:45 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-342706210  

> You can see some of the partial results here  
https://github.com/vinniefalco/beast/commits/uri  
  
Wow, that looks like quite a bit of effort. Any estimate on when that will be merged into master?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-11-08 07:42:43 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-342735995  

It is far from ready. A few months?

---

## Comment 7

> Username: bsergeev  
> Created at: 2018-03-26 18:10:14 UTC  
> Updated at: 2018-03-26 18:12:54 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-376259849  

What's wrong with a "simple" regex?  
E.g. something like this:  
```cpp  
#include <boost/algorithm/string.hpp> // for case-insensitive string comparison  
...  
struct ParsedURI {  
  std::string protocol;  
  std::string domain;  // only domain must be present  
  std::string port;  
  std::string resource;  
  std::string query;   // everything after '?', possibly nothing  
};  
  
ParsedURI parseURI(const std::string& url) {  
  ParsedURI result;  
  auto value_or = [](const std::string& value, std::string&& deflt) -> std::string {  
    return (value.empty() ? deflt : value);  
  };  
  // Note: only "http", "https", "ws", and "wss" protocols are supported  
  static const std::regex PARSE_URL{ R"((([httpsw]{2,5})://)?([^/ :]+)(:(\d+))?(/([^ ?]+)?)?/?\??([^/ ]+\=[^/ ]+)?)",   
                                     std::regex_constants::ECMAScript | std::regex_constants::icase };  
  std::smatch match;  
  if (std::regex_match(url, match, PARSE_URL) && match.size() == 9) {  
    result.protocol = value_or(boost::algorithm::to_lower_copy(std::string(match[2])), "http");  
    result.domain   = match[3];    
    const bool is_sequre_protocol = (result.protocol == "https" || result.protocol == "wss");  
    result.port     = value_or(match[5], (is_sequre_protocol)? "443" : "80");  
    result.resource = value_or(match[6], "/");  
    result.query = match[8];  
    assert(!result.domain.empty());  
  }  
  return result;  
}  
```

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-03-26 18:21:34 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-376263406  

>What's wrong with a "simple" regex?  
  
That's perfectly fine for a lot of cases but not something suitable for standardization... there's a lot of memory allocations there.

---

## Comment 9

> Username: trivigy  
> Created at: 2018-04-30 20:05:56 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-385512833  

@vinniefalco Any updates on when you feel like it might get merged into master?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-04-30 20:38:07 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-385521291  

Nothing in sight, sorry!

---

## Comment 11

> Username: coneiric  
> Created at: 2018-08-20 18:42:05 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-414420650  

@vinniefalco this is awesome work, and I'd like to help. Are there any low-hanging fruit style issues, or areas/tasks good for a new contributor?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-08-20 18:50:06 UTC  
> Updated at: 2018-08-20 18:50:15 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-414423119  

Well great question. There is this lovely pull request: https://github.com/boostorg/beast/pull/1227  
  
Unfortunately it needs a **lot** of work, which I will do if the author is not able to but it will take time. He implemented a URL parser which is not ideal but I think it is suitable for the beast/experimental directory. We need to extract his URL parser and polish it up to the level of quality needed to exist in beast/experimental. This means it needs the right file structure, Javadocs, correct interface, and documentation entries. But most importantly it needs tests. If you want to take a stab at it, go for it. Otherwise I will start it soon.  
  
If you take this on, I can guide you on the necessary cleanups.

---

## Comment 13

> Username: coneiric  
> Created at: 2018-08-20 22:38:29 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-414486117  

Will start reviewing the code in that PR, and pay close attention to the parser. After a first read, will come up with some test cases, and try to get something working.  
  
Thanks for the direction, and offer to guide. Will check back in as progress is made.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2018-08-20 23:34:28 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-414497836  

The first thing to do is to extract the files and sort them into <boost/beast/experimental/core/*> using the same style as Beast (detail namespace for private interfaces, .ipp file in impl/ directory for function definitions, files in detail/ directory if they are entirely private). I'm not quite sure if the parser has its own set of error_code but if it does it needs to use the same style as Beast. If it doesn't have error codes that's okay for a first draft.

---

## Comment 15

> Username: coneiric  
> Created at: 2018-08-21 22:12:02 UTC  
> Updated at: 2018-08-22 03:09:49 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-414838719  

Thanks for the heads up, I didn't see your message before getting started. Will move the files into the proper structure. I wrote some basic tests based on your URI branch, and will move those to the proper place as well.  
  
**edit:** Made the changes, and they're ready to PR in my [develop branch](https://github.com/boostorg/beast/compare/develop...coneiric:develop). Should I PR now, or wait until refactoring the draft (improving parser state machine, using isalpha/isdigit from your URI branch, implementing error codes, etc.)?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2018-08-22 03:01:55 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-414893012  

Travis builds codecov reports so we can ensure that every line is tested:  
https://codecov.io/gh/boostorg/beast/list/master/

---

## Comment 17

> Username: xim  
> Created at: 2019-02-06 21:53:13 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-461202757  

A boost::beast-based project I'm working on is using a custom URI parser with known bugs, and I wouldn't mind giving this parser a try rather than first using another library, then moving to this once it's stable. Keeping the dependency list to only boost would be great. Any progress in sight, or shouldn't I hold my breath?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2019-02-06 22:04:11 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-461206310  

I won't be able to get to this until the end of the year so I wouldn't wait if I were you.

---

## Comment 19

> Username: maingoh  
> Created at: 2019-11-12 10:47:31 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-552840148  

There is a new project from cppnetlib which seem quite complete. Might worth including it in beast ? https://github.com/cpp-netlib/url

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-11-12 11:43:17 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-552859099  

I will look at it!

---

## Comment 21

> Username: pancpp  
> Created at: 2020-10-07 07:29:24 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-704751002  

Any news on this topic other than COVID?

---

## Comment 22

> Username: ivan-volnov  
> Created at: 2021-01-21 11:03:37 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-764557174  

I'm looking forward to have this feature implemented

---

## Comment 23

> Username: vinniefalco  
> Created at: 2021-01-21 11:04:41 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-764557788  

Yes, it is being worked on: https://github.com/CPPAlliance/url

---

## Comment 24

> Username: ivan-volnov  
> Created at: 2021-01-21 11:12:33 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-764562133  

Thanks for the info!

---

## Comment 25

> Username: xim  
> Created at: 2022-01-04 21:51:19 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-1005196899  

How mature is Boost.URL now? I couldn't find a roadmap, but it sure looks like it's being worked on actively, great work. Could I use it in a project that accepts external input and expect it not to explode, bug and security wise?

---

## Comment 26

> Username: madmongo1  
> Created at: 2022-01-04 22:03:48 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-1005203991  

I should think it's fit for purpose. You'll need to include it manually for the time-being, but that shouldn't be an issue.

---

## Comment 27

> Username: vinniefalco  
> Created at: 2022-01-04 23:40:49 UTC  
> Updated at: 2022-01-04 23:41:43 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-1005255416  

> How mature is Boost.URL now?  
  
`url_view` and `authority_view` are great.  
  
`url` is good, but there could be a lingering bug or two with the modification functions (or not).  
  
We're working on the docs, and polish for the low-level parsing functions (which you very likely don't need or care about). If you have questions please open an issue in the URL repo, or visit us in the #boost-url on The C++Slack  (https://cppalliance.org/slack/)

---

## Comment 28

> Username: vinniefalco  
> Created at: 2022-06-16 16:30:55 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-1157881342  

This is very usable now https://github.com/CPPAlliance/url and it is waiting for a Boost formal review.

---

## Comment 29

> Username: loupus  
> Created at: 2022-12-13 20:10:59 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-1349624630  

auto res = boost::urls::parse_uri("https://somehost/somepath/somequery?dateCreated:>2022-05-16T21:00:00Z");  
  
iterator does not go after >

---

## Comment 30

> Username: vinniefalco  
> Created at: 2022-12-13 20:26:39 UTC  
> Updated at: 2022-12-13 20:26:49 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-1349641205  

">" is not a valid character, and must be percent-escaped. Try `%3E` instead of >

---

## Comment 31

> Username: loupus  
> Created at: 2022-12-14 10:27:26 UTC  
> Updated at: 2022-12-14 10:29:10 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-1350817018  

thank you,  do you think of unreserved_chars parametrized boost::urls::parse_uri function, like boost::urls::encode

---

## Comment 32

> Username: alandefreitas  
> Created at: 2022-12-14 16:54:39 UTC  
> Updated at: 2022-12-14 16:55:59 UTC  
> Url: https://github.com/boostorg/beast/issues/787#issuecomment-1351765486  

```cpp  
auto res = boost::urls::parse_uri("https://somehost/somepath/somequery?dateCreated:>2022-05-16T21:00:00Z");  
```  
  
won't work because `"https://somehost/somepath/somequery?dateCreated:>2022-05-16T21:00:00Z"` is an invalid URL.  
  
The encoded string would be  
  
```cpp  
auto res = boost::urls::parse_uri("https://somehost/somepath/somequery?dateCreated:%3E2022-05-16T21:00:00Z");  
```  
  
`boost::urls::parse_uri` can't try to be smart and fix the URLs for the user because it returns a `url_view`, which needs to point to a string that already exists.  
  
What you _can_ do to achieve that is use the setters:  
  
```cpp  
url u = parse_uri("https://somehost/somepath/somequery").value();  
u.set_query("dateCreated:>2022-05-16T21:00:00Z");  
```  
  
because `set_query` will encode whatever needs to be encoded.  
  
```cpp  
url u = parse_uri("https://somehost/somepath/somequery").value();  
u.set_encoded_query("dateCreated:>2022-05-16T21:00:00Z");  
```  
  
would also work because it reencodes invalid characters.  
  
In the near future, you will also be able to use the `format` function being proposed in https://github.com/boostorg/url/pull/634:  
  
```cpp  
url u = format("https://somehost/somepath/somequery?{}", "dateCreated:>2022-05-16T21:00:00Z");  
```  
  
because `format` identifies to which component each replacement id `{}` belongs and reencodes whatever needs to be reencoded when applying the replacements.
