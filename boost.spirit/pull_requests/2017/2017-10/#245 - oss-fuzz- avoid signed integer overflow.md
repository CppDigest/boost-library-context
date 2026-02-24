# #245 oss-fuzz: avoid signed integer overflow [Merged]

> Username: dtardon  
> Created at: 2017-10-02 14:43:27 UTC  
> Updated at: 2018-02-19 22:33:56 UTC  
> Merged at: 2017-10-07 09:08:01 UTC  
> Closed at: 2017-10-07 09:08:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/245  



---

## Comment 1

> Username: djowel  
> Created_at: 2017-10-03 02:44:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/245#issuecomment-333723190  

I'm not sure what to make of this. Please add a test case exhibiting the issue this PR is supposed to fix.

---

## Comment 2

> Username: dtardon  
> Created_at: 2017-10-03 18:18:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/245#issuecomment-333932858  

github doesn't let me attach a sample file, so I'll paste it... Compile with clang++/g++ with -fsanitize=undefined:  
   
 #include <string>  
 #include <boost/spirit/include/qi.hpp>  
   
 int main()  
 {  
     namespace qi = boost::spirit::qi;  
     const std::string s = "0E-2147483648";  
     std::string::const_iterator it = s.begin();  
     qi::phrase_parse(it, s.end(), qi::double_, qi::space);  
 }

---

## Comment 3

> Username: dtardon  
> Created_at: 2017-10-03 18:20:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/245#issuecomment-333933528  

And that didn't work too well either, so here it is again: https://paste.fedoraproject.org/paste/rs6RUHcwrXvY3oJeTgyHjQ .

---

## Comment 4

> Username: djowel  
> Created_at: 2017-10-03 22:32:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/245#issuecomment-333998388  

Are all the Qi tests still passing with this change?

---

## Comment 5

> Username: dtardon  
> Created_at: 2017-10-05 14:18:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/245#issuecomment-334479115  

@djowel - Yes, they do.

---
