# #59 - Fix RE2C warnings [Closed]

> Username: jefftrull  
> Created at: 2020-01-13 22:49:31 UTC  
> Updated at: 2020-03-19 04:22:04 UTC  
> Closed at: 2020-03-19 04:22:04 UTC  
> Url: https://github.com/boostorg/wave/issues/59  

In 2015 RE2C gained the ability to analyze its input files for potential issues. When run on the cpplexer's control file with `re2c -b -o cpp_re.inc -W cpp.re` it flags two kinds of issues:  
  
1. "Undefined control flow", which the docs call "the most dangerous and common mistake", plus  
2. "Match empty string", which can result in an infinite loop.  
  
```  
cpp.re:451:6: warning: control flow is undefined for strings that match   
        '[\x0-\x2D\x2F\x3A-\xFF]'  
        '\x2E [\x0-\x2F\x3A-\xFF]'  
, use default rule '*' [-Wundefined-control-flow]  
cpp.re:459:6: warning: control flow is undefined for strings that match   
        '[\x0-\x2D\x2F\x3A-\xFF]'  
        '\x2E [\x0-\x2F\x3A-\xFF]'  
, use default rule '*' [-Wundefined-control-flow]  
cpp.re:472:12: warning: rule matches empty string [-Wmatch-empty-string]  
cpp.re:481:12: warning: rule matches empty string [-Wmatch-empty-string]  
cpp.re:495:6: warning: control flow is undefined for strings that match '[\x0-\x8\xE-\x1F]', use default rule '*' [-Wundefined-control-flow]  
cpp.re:507:6: warning: control flow is undefined for strings that match '[\x0-\x8\xE-\x1F]', use default rule '*' [-Wundefined-control-flow]  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-03-18 19:26:30 UTC  
> Url: https://github.com/boostorg/wave/issues/59#issuecomment-600816959  

I've done some more investigation now.  
  
The first two rules are because only numbers are described in the `pp_number` subscanner, and so any other input encountered would be undefined. However, the subscanner is only invoked for the `"."? Digit` pattern, and the cursor is reset, so this is guaranteed to work.  
  
The middle two are in the `integer_suffix` subscanner and match the empty string because an `Integer` has already been scanned, so this is OK.  
  
The last two are in the `extcharlit` and `extstrlit` subscanners. It looks like an embedded control character after the preamble will result in undefined (according to RE2C, not the Standard) behavior.

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-03-18 19:30:15 UTC  
> Updated at: 2020-03-18 19:30:34 UTC  
> Url: https://github.com/boostorg/wave/issues/59#issuecomment-600818548  

I propose that:  
  
1. we add catchall patterns that fire assertions for the first two warnings. They should not fire now, but will add some peace of mind in case the code evolves.  
2. we throw lexer exceptions for the last two in the same manner that is already done for `extrawstringlit` [here](https://github.com/boostorg/wave/blob/b68940a83b34ba8b28342fb969fa8c68978e8cbe/include/boost/wave/cpplexer/re2clex/cpp.re#L516)  
  
If there are no objections I will create a PR to that effect.
