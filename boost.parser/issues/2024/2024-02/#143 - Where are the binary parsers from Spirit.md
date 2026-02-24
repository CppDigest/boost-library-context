# #143 - Where are the binary parsers from Spirit? [Open]

> Username: LegalizeAdulthood  
> Created at: 2024-02-28 19:42:42 UTC  
> Updated at: 2024-03-06 18:02:03 UTC  
> Url: https://github.com/boostorg/parser/issues/143  

Spirit V2 had parsers for binary values.  When parsing binary file formats, e.g. PNG image file format, numeric values are present as binary encodings using a particular byte order.  IMO, this was one of the things that set Spirit apart from other parsing frameworks.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-28 23:56:09 UTC  
> Url: https://github.com/boostorg/parser/issues/143#issuecomment-1970125954  

I never used this, and aside from Michael Caisse I never met anyone that did.  It seemed like little bang for a lot of buck.  I'll leave this open, and I may do it one day, but it's a pretty low priority.

---

## Comment 2

> Username: LegalizeAdulthood  
> Created at: 2024-03-06 18:02:02 UTC  
> Url: https://github.com/boostorg/parser/issues/143#issuecomment-1981484771  

As I say, it's useful for parsing binary file formats.  It's a bit of a chicken-and-egg situation -- parser frameworks don't typcially provide facilities for doing it, so the people parsing binary file formats don't use parser frameworks and they don't show up in reviews of parser frameworks `:)`.  I'd be content with an outline of an example of how to roll my own.
