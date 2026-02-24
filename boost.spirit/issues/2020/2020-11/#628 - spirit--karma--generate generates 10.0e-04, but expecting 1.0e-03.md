# #628 - spirit::karma::generate generates 10.0e-04, but expecting 1.0e-03 [Closed]

> Username: markusjrmueller  
> Created at: 2020-11-20 10:19:19 UTC  
> Updated at: 2021-01-04 00:03:17 UTC  
> Closed at: 2021-01-04 00:03:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/628  

Code is:  
  
```  
std::string s;  
double value = 0.00099999999999999829;  
std::back_insert_iterator<std::string> sink(s);  
boost::spirit::karma::generate(sink, boost::spirit::karma::real_generator<>(), value);  
std::cout << s << std::endl; // outputs 10.0e-04  
```

---

## Comment 1

> Username: hkaiser  
> Created at: 2020-11-20 15:04:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/628#issuecomment-731222655  

The 64 bit IEEE floating point format supports only up to 15.9 digits of precision (see https://en.wikipedia.org/wiki/Double-precision_floating-point_format). The number you are trying to convert requires at least 17 digits in order to be represented. The behavior of the generator is undefined in those cases. Please see https://github.com/boostorg/spirit/issues/529 for more details.

---

## Comment 2

> Username: markusjrmueller  
> Created at: 2020-11-21 08:28:58 UTC  
> Updated at: 2020-11-21 11:34:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/628#issuecomment-731527918  

I am using MSCV from visual studio 2017 in 64bit. The value comes out when doing some math. They claim to be IEEE conform when not compiling with the compiler flag /fast. At the moment I use the precise-flag.  
I did tests with a precision of 6 and even changed the code according to #529 but nothing changes.  
I do not know what I can do to fix this issue when using karma.

---

## Comment 3

> Username: hkaiser  
> Created at: 2020-11-21 22:00:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/628#issuecomment-731643013  

@markusjrmueller please see #629 for a possible fix. Please let us know if this resolves your issue.
