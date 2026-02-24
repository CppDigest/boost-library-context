# #71 - Encoding error in generated page [Open]

> Username: phuclv90  
> Created at: 2020-03-12 17:01:51 UTC  
> Updated at: 2020-03-12 17:01:51 UTC  
> Url: https://github.com/boostorg/format/issues/71  

In [this page](https://www.boost.org/doc/libs/1_72_0/libs/format/example/sample_new_features.cpp) the output is broken because the source code file uses ISO-8859-1 but is assumed to be UTF-8 when copying to the web page. Github [guesses the correct encoding](https://github.com/boostorg/format/blob/7f0b2fbb3b8b4776817af7302e848dacc607786d/example/sample_new_features.cpp) so it displays properly  
  
The server must be fixed to read the file in the correct encoding, or just convert all files to UTF-8. I think the latter is the best solution  
  
![image](https://user-images.githubusercontent.com/9285512/76545535-866ad900-64bc-11ea-948e-475116d5b1de.png)
