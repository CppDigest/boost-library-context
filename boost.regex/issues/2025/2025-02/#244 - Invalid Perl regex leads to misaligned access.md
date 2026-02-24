# #244 - Invalid Perl regex leads to misaligned access [Closed]

> Username: cmazakas  
> Created at: 2025-02-18 22:13:31 UTC  
> Updated at: 2025-02-28 12:58:03 UTC  
> Closed at: 2025-02-28 12:58:03 UTC  
> Url: https://github.com/boostorg/regex/issues/244  

The following code:  
```cpp  
int main()  
{  
   // char const strdata[] = "\x00t\x03.z%(?x:]*+\x0c#\\x0c\x0c\x0c+\x0c#\\x0c\x0c\x0c\x11\x0c\x0c\xff\xff\xfd*\xff\xff\xff\xff\xff\xff\xff\xff|\xff\xff\xfd*\xff\xff)*\x01\x03\x00\x00\x00\x03\xff\xff\xff\x00\x00\xff\xff\xff";  
   char const strdata[] = "(?x:]*+#\x0c+)*";  
     
   std::string str(strdata, strdata + sizeof(strdata) - 1);  
   boost::regex e(str);  
   std::string text = "hello, world!";  
  
   boost::match_results<std::string::const_iterator> what;  
   bool match = boost::regex_match(text, what, e,  
                      boost::match_default | boost::match_partial);  
   (void)match;  
}  
```  
will cause the following runtime failure with ubsan:  
```  
boost/regex/v5/basic_regex_creator.hpp:717:21: runtime error: member access within misaligned address 0x51d0000001b7 for type 're_syntax_base', which requires 8 byte alignment  
0x51d0000001b7: note: pointer points here  
 00 00 01 be be  00 00 00 00 00 00 00 00  b7 01 00 00 d0 51 00 00  00 00 00 00 00 01 be be  00 00 00  
```  
  
We erroneously accept this regex. Perl fails with:  
```  
Unmatched ( in regex; marked by <-- HERE in m/( <-- HERE ?x:]*+#\x0c+)*/ at test.pl line 3.  
```  
  
when given:  
```perl  
"rawr" =~ /(?x:]*+#\x0c+)*/  
```

---

## Comment 1

> Username: cmazakas  
> Created at: 2025-02-18 23:42:57 UTC  
> Url: https://github.com/boostorg/regex/issues/244#issuecomment-2667171196  

This manifests in CI here: https://github.com/cmazakas/regex/actions/runs/13401006696/job/37431641879#step:15:387
