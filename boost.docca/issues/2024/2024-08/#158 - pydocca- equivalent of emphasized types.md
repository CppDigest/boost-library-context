# #158 - pydocca: equivalent of emphasized types [Closed]

> Username: anarthal  
> Created at: 2024-08-14 13:13:30 UTC  
> Updated at: 2024-10-03 16:29:19 UTC  
> Closed at: 2024-10-03 16:29:19 UTC  
> Url: https://github.com/boostorg/docca/issues/158  

I'm using emphasized types (in the old docca) to provide linking for concepts in type parameters. E.g. for a function declaration like:  
  
```  
template <class Formattable>  
void format(Formattable f);  
```  
  
The `Formattable` type in the template argument list got replaced by a link to the page describing the concept.  
  
For an example, see: https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__format_sql.html  
  
It looks like `replace_strings` is the closest functionality, but this applies to every phrase in the discussion, and doesn't respect word boundaries. With terms like `Stream`, this causes problems.  
  
What would be the best path for this?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-08-14 16:04:54 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2289203866  

docca has a configurable xsl file which lists each concept and its symbol, and performs the substitution automatically I thought?

---

## Comment 2

> Username: anarthal  
> Created at: 2024-08-14 18:16:26 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2289518023  

If you're talking about emphasized types, this would replace `Formattable` by `__Formattable__`, and then a Quickbook macro performs the actual replacement. I don't know if there's anything else I'm not aware of.  
  
This issue is about the Python version of docca, though.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-08-15 03:43:49 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2290507188  

So, there are multiple ways we could make this more usable.  
1. Change replacement logic to only work in certain contexts. E.g. Type names and monospaced spans.  
2. Add regular expression support to Jinja templates, so that matching would respect word boundaries.  
  
I don't think we need both. I lean towards 1, because 2 would probably not be enough anyway. So, we need to enumerate the desired contexts where we want substitutuions.

---

## Comment 4

> Username: anarthal  
> Created at: 2024-08-15 08:01:36 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2290850199  

1 looks better. I'm happy with having a `replacement_types` config or similar. I think that should still respect word boundaries, since I wouldn't want `StreamHandle` to get `Stream` replacement.

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-08-17 07:41:47 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2294754954  

> ... since I wouldn't want `StreamHandle` to get `Stream` replacement.  
  
Huh, I haven't considered this.

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-08-17 07:55:52 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2294769093  

Now, that I have looked into implementing option 1, I realised I have a sort of solution already. Instead of matching `Stream`, match `class Stream` (or better yet `\bclass Stream\b`). I kinda think that this should be enough.

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-08-17 08:37:57 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2294795261  

#164 should allow this with e.g.  
```json  
{ "replace_strings":  
  { "\\bclass Formattable\\b":  "class __Formattable__"  
  , "\\bclass Stream\\b":  "class __Stream__"  
  , "\\bclass StreamHandle\\b":  "class __StreamHandle__"  
  }  
}  
```  
  
If you think, this is not enough, I can change the replacement to only happen in code contexts (which includes both monospaced spans and entity declarations in code blocks. This would make it less universal, but would also significantly reduce the likelihood of undesired matches.

---

## Comment 8

> Username: anarthal  
> Created at: 2024-08-17 11:26:11 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2294832114  

That makes sense. I'll have a try when I'm back.

---

## Comment 9

> Username: anarthal  
> Created at: 2024-09-01 15:02:45 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2323382211  

This only works for non-variadic template parameters, unfortunately. Variadic packs are reported by Doxygen inconsistently, once again:  
  
```  
        <templateparamlist>  
          <param>  
            <type>class SpanElementType</type>  
          </param>  
          <param>  
            <type>class...</type>  
            <declname>StaticRow</declname>  
            <defname>StaticRow</defname>  
          </param>  
        </templateparamlist>  
```  
  
This makes the obvious solution of adding `"\\bclass\\.\\.\\. StaticRow\\b": "class... __StaticRow__"` to the config file replacements not work.

---

## Comment 10

> Username: anarthal  
> Created at: 2024-09-02 06:21:39 UTC  
> Url: https://github.com/boostorg/docca/issues/158#issuecomment-2323904753  

Note that substituting raw `StaticRow` doesn't work either, because parameter names are not passed to the substitution function: see https://github.com/boostorg/docca/blob/5f349f0c7b81efa6f26cd1014cc15f5addfbbc87/include/docca/quickbook/components.jinja2#L471
