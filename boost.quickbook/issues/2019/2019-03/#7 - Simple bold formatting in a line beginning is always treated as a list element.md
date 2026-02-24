# #7 - Simple bold formatting in a line beginning is always treated as a list element [Open]

> Username: Kojoley  
> Created at: 2019-03-28 21:57:39 UTC  
> Updated at: 2019-03-29 00:28:30 UTC  
> Url: https://github.com/boostorg/quickbook/issues/7  

The examples in docs are clearly shows that there are some issues with bold formatting https://www.boost.org/doc/libs/1_69_0/doc/html/quickbook/syntax/phrase.html#quickbook.syntax.phrase.line_break  
  
![image](https://user-images.githubusercontent.com/2743474/55194987-336d1880-51bc-11e9-896e-8d554880d2e1.png)

---

## Comment 1

> Username: danieljames  
> Created at: 2019-03-28 22:35:59 UTC  
> Url: https://github.com/boostorg/quickbook/issues/7#issuecomment-477796041  

An asterisk at the start of a line has been documented to have this behaviour since the beginning, see:  
  
https://github.com/boostorg/quickbook/blob/84c14dd51753a43c69c7bbe1873d5186627741f1/doc/quickbook.qbk#L136  
  
So I don't think it's a good idea to change it.  
  
The examples broke because lists are now supported in table cells, they aren't in earlier versions of the language. But it was probably a mistake to interpret it as a list if there isn't a newline before the asterisk. For example:  
  
    [*bold*]  
    [  
    *list  
    ]  
  
The first table cell should probably contain bold text, not a list item. That would fix the examples.

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-03-28 22:52:46 UTC  
> Url: https://github.com/boostorg/quickbook/issues/7#issuecomment-477799971  

> An asterisk at the start of a line has been documented to have this behaviour since the beginning, see:  
  
Actually I am not sure why it is so strict, and in:  
```  
*bold*  
* list item, bla bla  
  *bold*  
```  
bold is not bold  
  
Consider markdown which is used on here, on GitHub (though, single asterisk here are italic):  
  
*bold*  
* list item, bla bla  
  *bold*

---

## Comment 3

> Username: danieljames  
> Created at: 2019-03-28 23:02:34 UTC  
> Url: https://github.com/boostorg/quickbook/issues/7#issuecomment-477802150  

I don't know, it predated me. I generally tried not to change this kind of thing to ease the transition between language versions. I guess markdown only interprets an asterisk as a list if there's a space after it.

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-03-28 23:08:13 UTC  
> Url: https://github.com/boostorg/quickbook/issues/7#issuecomment-477803427  

> I guess markdown only interprets an asterisk as a list if there's a space after it.  
  
Yes, and it is very intuitive.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-03-29 00:28:30 UTC  
> Url: https://github.com/boostorg/quickbook/issues/7#issuecomment-477819410  

Tried to upgrade Spirit docs from 1.5 to 1.7, the bold issue hit me here:  
```qbk  
[tip  *For sequences only:* __x3__ exposes a set of API functions  
      ...  
]  
```
