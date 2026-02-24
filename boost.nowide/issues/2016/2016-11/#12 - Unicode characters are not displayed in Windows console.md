# #12 - Unicode characters are not displayed in Windows console [Closed]

> Username: mmatrosov  
> Created at: 2016-11-12 18:32:01 UTC  
> Updated at: 2016-11-12 18:34:10 UTC  
> Closed at: 2016-11-12 18:34:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/12  

I have a simplest program, that writes a unicode character to cout:  
  
    boost::nowide::cout << u8"Hello, \u03A8!" << std::endl;  
  
If I stream output to a file, I see the correct answer: "Hello, Ψ!". But if I run the program in console, I see a question mark instead: "Hello, ?!". I thought than no additional manipulations with locales is needed why I use boost.nowide. Is this a bug, or should I set up something?

---

## Comment 1

> Username: mmatrosov  
> Created at: 2016-11-12 18:34:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/12#issuecomment-260139859  

This answer helped: http://stackoverflow.com/a/388500/261217  
  
It turned out I need to set console font to Lucida Console. Seems like default font just does not know how to render unicode characters.
