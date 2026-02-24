# #6 - Unexpected character warning [Open]

> Username: Kojoley  
> Created at: 2018-12-18 21:23:40 UTC  
> Updated at: 2018-12-18 21:23:40 UTC  
> Url: https://github.com/boostorg/quickbook/issues/6  

I am trying to fix Spirit docs https://github.com/boostorg/spirit/blob/develop/doc/lex/lexer.qbk#L34, but having troubles with bogus warning or broken escaping.  
  
Given:  
```  
    [[`"[xyz]\"foo"`]  [The literal string `[xyz]"foo`]]  
```  
  
Renders correctly:  
![image](https://user-images.githubusercontent.com/2743474/50182918-82c3c300-0308-11e9-84a9-e78acf7a73d0.png)  
  
But generates a warning:  
```  
lex/lexer.qbk:34: warning: in column:50, unexpected character: "  
```  
  
Fixing it like this:  
```  
    [[`"[xyz]\"foo"`]  [The literal string `[xyz]\"foo`]]  
```  
  
Does not eliminate it:  
```  
lex/lexer.qbk:34: warning: in column:51, unexpected character: "  
```  
  
And leads to incorrect rendering:  
![image](https://user-images.githubusercontent.com/2743474/50183056-ee0d9500-0308-11e9-9644-03a73f82d1de.png)
