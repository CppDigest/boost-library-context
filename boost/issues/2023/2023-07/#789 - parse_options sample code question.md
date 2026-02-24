# #789 - parse_options sample code question [Closed]

> Username: johncblacker  
> Created at: 2023-07-01 20:35:54 UTC  
> Updated at: 2023-07-04 17:24:56 UTC  
> Closed at: 2023-07-04 17:24:32 UTC  
> Url: https://github.com/boostorg/boost/issues/789  

I followed the doc included in the json library for setting the parse_option to allow comments, but now my program won't compile,.  
Here's what I added:  
parse_options opt;                                  // all extensions default to off  
opt.allow_comments = true;                          // permit C and C++ style comments to appear in whitespace  
json::value  
parse_file( char const* filename, opt );  // I added the "opt" here  
  
The build errors I get - suspect the first on is the culprit...  
../pretty.cpp: At global scope:  
../pretty.cpp:25:1: error: ‘parse_options’ does not name a type  
   25 | parse_options opt;                                  // all extensions default to off  
      | ^~~~~~~~~~~~~  
../pretty.cpp:26:1: error: ‘opt’ does not name a type  
   26 | opt.allow_comments = true;                          // permit C and C++ style comments to appear in whitespace  
      | ^~~  
../pretty.cpp:29:35: error: ‘opt’ has not been declared  
   29 | parse_file( char const* filename, opt );  
      |                                   ^~~  
../pretty.cpp:30:1: error: expected unqualified-id before ‘{’ token  
   30 | {  
      | ^  
../pretty.cpp: In function ‘int main(int, char**)’:  
../pretty.cpp:151:45: error: too few arguments to function ‘boost::json::value parse_file(const char*, int)’  
  151 |         auto const jv = parse_file( argv[1] );  
      |                                             ^  
  
I don't know if I'm missing an include; or what might be wrong...I need some guidance.

---

## Comment 1

> Username: johncblacker  
> Created at: 2023-07-04 17:24:32 UTC  
> Url: https://github.com/boostorg/boost/issues/789#issuecomment-1620559276  

resolved the compile problem by moving the "parse_options opt;" and "opt.allow_comments=true;" statements into the "parse_file" function.  
Problem/issue will be closed.

---

## Comment 2

> Username: johncblacker  
> Created at: 2023-07-04 17:24:55 UTC  
> Url: https://github.com/boostorg/boost/issues/789#issuecomment-1620559538  

Closed, fixed per above comment.
