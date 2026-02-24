# #249 - Various disagreements between boost::regex and the JS spec [Open]

> Username: Alcaro  
> Created at: 2025-05-15 12:53:35 UTC  
> Updated at: 2025-05-15 17:25:54 UTC  
> Url: https://github.com/boostorg/regex/issues/249  

I implemented a test suite for JS v3 regex syntax as applied to 8bit strings, applied it to the three std:: implementations, and found a lot of bugs. The same tests can also be run against Boost.  
  
https://godbolt.org/z/Pq76Pe1cz (some of the comments only apply to the regex engine I wrote alongside the tests, feel free to ignore them)  
  
<details><summary>List of behavioral differences</summary>  
  
```  
FAIL should compile [a-b-c-d]  
FAIL should compile [a-b-c-d]  
FAIL should compile []  
FAIL should compile [^]  
FAIL expected error a[A-]]c  
FAIL expected error ]  
FAIL expected error \c+  
FAIL expected error [a-\w]  
FAIL \Z :: Z :: ret=(fail) :: exp=Z  
FAIL expected error \x0  
FAIL [\W\D] :: a :: ret=(fail) :: exp=a  
FAIL expected error \00  
FAIL expected error \01  
FAIL expected error [\01]  
FAIL expected error [\00]  
FAIL expected error [\0-\00]  
FAIL expected error a++  
FAIL expected error a{,5}  
FAIL expected error {  
FAIL expected error }  
FAIL expected error a{  
FAIL expected error a{1  
FAIL expected error a{1,  
FAIL (abc)?\1 ::  :: ret=(fail) :: exp=/(null)  
FAIL (abc)?\1 :: abc :: ret=(fail) :: exp=/(null)  
FAIL (?:(a)|b)\1c :: bc :: ret=(fail) :: exp=bc/(null)  
FAIL (?:a|(b))\1c :: ac :: ret=(fail) :: exp=ac/(null)  
FAIL (a?){0,2}\1b :: b :: ret=b/ :: exp=b/(null)  
FAIL (a?){0,2}\1b :: ab :: ret=ab/ :: exp=(fail)  
FAIL (a?){0,5} :: aa :: ret=aa/ :: exp=aa/a  
FAIL (?:(a)|b)\1 :: b :: ret=(fail) :: exp=b/(null)  
FAIL (?:(a)b|aa)\1 :: aaa :: ret=(fail) :: exp=aa/(null)  
FAIL expected error (a)b\01c  
FAIL ((a)|(b))+ :: ab :: ret=ab/b/a/b :: exp=ab/b/(null)/b  
FAIL ((a)|(b))+ :: ba :: ret=ba/a/a/b :: exp=ba/a/a/(null)  
FAIL ((a)|(b)){2} :: ab :: ret=ab/b/a/b :: exp=ab/b/(null)/b  
FAIL ((a)|(b)){2} :: ba :: ret=ba/a/a/b :: exp=ba/a/a/(null)  
FAIL ((a)\2|(b)\3){2} :: aabb :: ret=aabb/bb/a/b :: exp=aabb/bb/(null)/b  
FAIL ((a)\2|(b)\3){2} :: bbaa :: ret=bbaa/aa/a/b :: exp=bbaa/aa/a/(null)  
FAIL (?:(a)|(b)|(c)|(d))+ :: abcd :: ret=abcd/a/b/c/d :: exp=abcd/(null)/(null)/(null)/d  
FAIL (?:(a)|(b)|(c)|(d))+c :: abc :: ret=abc/a/b/(null)/(null) :: exp=abc/(null)/b/(null)/(null)  
FAIL (a?)* ::  :: ret=/ :: exp=/(null)  
FAIL (a?){2,4}b\1c :: aabc :: ret=aabc/ :: exp=(fail)  
FAIL ((b)?(be)?){0,5}bbee :: bbebbee :: ret=bbebbee//b/be :: exp=bbebbee/bbe/b/be  
FAIL ^(ab|()|a|bc)*$ :: abc :: ret=abc// :: exp=abc/bc/(null)  
FAIL (?!(?!(a))) :: a :: ret=/a :: exp=/(null)  
FAIL expected error (?=a)+  
FAIL (?!(a))\1 :: b :: ret=(fail) :: exp=/(null)  
FAIL (?:(a)|\1b)+ :: aabbaab :: ret=aa/a :: exp=aabbaab/(null)  
FAIL (?:(a)|\1b)+ :: baabbaab :: ret=(fail) :: exp=baabbaab/(null)  
FAIL should compile (?=)a  
FAIL should compile (?![])  
FAIL should compile (?![])  
FAIL should compile (?![^])  
FAIL should compile (?![^])  
```  
  
</details>  
  
I can't find a clear specification for what regex syntax Boost tries to implement (https://www.boost.org/doc/libs/1_87_0/libs/regex/doc/html/boost_regex/ref/syntax_option_type/syntax_option_type_perl.html says it's supposed to match JS, but I can't find which version, and https://www.boost.org/doc/libs/1_87_0/libs/regex/doc/html/boost_regex/syntax/perl_syntax.html lists several Perl-only features).  
  
As such, I don't know which of the above divergences are bugs, and which are expected; feel free to consider this a meta-bug, and close it once the above have been filed as separate bugs as appropriate. (For example, `]` is explicitly called out as legal in [docs](https://www.boost.org/doc/libs/1_87_0/libs/regex/doc/html/boost_regex/syntax/perl_syntax.html), the two `[a-b-c-d]` are the same bug, and `[\W\D]` is #241.)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-05-15 17:25:53 UTC  
> Url: https://github.com/boostorg/regex/issues/249#issuecomment-2884561100  

The intention for Boost is to follow Perl, but of course that is an ever randomly changing target, so I suspect we're some way behind, and in general I only tend to change things when bug reports come in :(
