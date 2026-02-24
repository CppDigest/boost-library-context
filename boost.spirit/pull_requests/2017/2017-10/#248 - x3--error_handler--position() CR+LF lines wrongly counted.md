# #248 x3::error_handler::position() CR+LF lines wrongly counted. [Merged]

> Username: m-dhooge  
> Created at: 2017-10-05 08:05:52 UTC  
> Updated at: 2017-10-10 02:43:05 UTC  
> Merged at: 2017-10-10 02:43:04 UTC  
> Closed at: 2017-10-10 02:43:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/248  

On systems where a line is terminated by CR+LF, the position() function only  
counts as one a sequence of empty lines.  
  
Because the most common line terminations (according to Wikipedia) are \n, \r  
and \r\n, I propose to only check previous char for \n. Unfortunately, this will  
more or less double the counted number of lines on any system using \n\r  
(but I don't this is worth doing something smarter!)  
  
MD

---

## Comment 1

> Username: djowel  
> Created_at: 2017-10-07 09:03:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/248#issuecomment-334921363  

I'm not sure I understand. Can you show some code that exhibits this behavior?

---

## Comment 2

> Username: m-dhooge  
> Created_at: 2017-10-09 08:39:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/248#issuecomment-335096174  

I created 3 couples of files in `x3_fun/test/parse_expression` (https://github.com/m-dhooge/x3_fun/tree/CR-LF/test/parse_expression). The input files dump as follows:  
```  
00000000  31 20 2b 20 32 20 2a 20  33 0d 0d 0d 34 20 2b 20  |1 + 2 * 3...4 + |  
00000010  35 0d                                             |5.|  
  
00000000  31 20 2b 20 32 20 2a 20  33 0d 0a 0d 0a 0d 0a 34  |1 + 2 * 3......4|  
00000010  20 2b 20 35 0d 0a                                 | + 5..|  
  
00000000  31 20 2b 20 32 20 2a 20  33 0a 0a 0a 34 20 2b 20  |1 + 2 * 3...4 + |  
00000010  35 0a                                             |5.|  
```  
When I run the parser tests, I get the following error (line is "2" for CRLF where it should be 4, like the other ones):  
```  
$ ./parse ../test/parse_expression  
=============================================  
==== Mismatch Found:  
==== File: "../test/parse_expression/EOL_is_CRLF.expect", Line: 1, Column: 59  
=============================================  
In file ../test/parse_expression/EOL_is_CRLF.input, line 2:  
Error! Expecting end of input here:  
4 + 5  
^_  
```  
In case your git messes up the line-endings, here is an archive:  
[EOL.tar.gz](https://github.com/boostorg/spirit/files/1367898/EOL.tar.gz)

---
