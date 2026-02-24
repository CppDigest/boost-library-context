# #223 Add fenced code blocks in documentation [Closed]

> Username: sdarwin  
> Created at: 2022-10-31 20:43:21 UTC  
> Updated at: 2023-01-12 00:27:34 UTC  
> Closed at: 2023-01-12 00:27:34 UTC  
> Url: https://github.com/boostorg/hof/pull/223  

Both the old and new Sphinx versions support fenced code blocks with backticks and the name of the highlighter. This is a way to configure the docs so they'll be compatible with myst-parser.    
The code that generated the conversion is here: https://gist.github.com/sdarwin/ef0964e135599b937dfde1de9c868dd2    
It's probably simpler and easier to run the transformation once and then it's done, rather than maintaining it in conf.py. Going forward, be sure to use the new syntax.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2023-01-11 17:04:37 UTC  
> Url: https://github.com/boostorg/hof/pull/223#issuecomment-1379182270  

@pfultz2 thanks for merging the other pull request.    
This one also needs to be added. Otherwise when upgrading Sphinx from 1.5.6 to 5.2.1 code blocks will be broken.  
The change is backwards compatible, since both the old and new versions can parse backticks.

---

## Comment 2

> Username: pfultz2  
> Created_at: 2023-01-11 17:31:39 UTC  
> Url: https://github.com/boostorg/hof/pull/223#issuecomment-1379241122  

I would prefer to not write the code ticks in the documentation and just tab indentation. Is there a way we can just convert it on the fly? Perhaps in `extract_doc`?

---

## Comment 3

> Username: sdarwin  
> Created_at: 2023-01-11 18:07:24 UTC  
> Url: https://github.com/boostorg/hof/pull/223#issuecomment-1379287947  

After upgrading, the new toolchain will be sphinx 5.2.1 and myst-parser.  What do these programs expect to see? They seem to be able to process the tick formatting and will render it correctly.    Tab indentation would be a special customization.  
  
Hof files contain tabbed code blocks in two formats which somewhat complicates things:    
- plain  
- inside of commented blocks preceded by "//"   
  
This script handles a one-time conversion:  https://gist.github.com/sdarwin/ef0964e135599b937dfde1de9c868dd2  
  
Presumably it would not be impossible to do that on-the-fly in extract_docs.  But (to me) such a task looks difficult.  It would need to be integrated with the other doc processing steps, and be able to deal with the two formats mentioned.   It seems more challenging to get that right compared to the one-time fix.

---

## Comment 4

> Username: pfultz2  
> Created_at: 2023-01-11 20:44:02 UTC  
> Url: https://github.com/boostorg/hof/pull/223#issuecomment-1379462417  

> This script handles a one-time conversion: https://gist.github.com/sdarwin/ef0964e135599b937dfde1de9c868dd2  
  
Interesting, why did you skip the concepts file?  
  
I opened #224 to convert to code fences on the fly. It seems the concepts page is not rendered completely, I am not sure why.

---

## Comment 5

> Username: sdarwin  
> Created_at: 2023-01-11 21:03:31 UTC  
> Updated_at: 2023-01-11 21:03:53 UTC  
> Url: https://github.com/boostorg/hof/pull/223#issuecomment-1379481031  

> why did you skip the concepts file?  
  
The concepts.md file already has code fences rather than tabs, so it didn't need to be reformatted.       
  
Also, I think the one-time script was getting confused when parsing concepts.md for some reason, since it contains tabs and spaces and so on.   The easy solution was to skip the file completely.

---

## Comment 6

> Username: pfultz2  
> Created_at: 2023-01-11 21:15:26 UTC  
> Url: https://github.com/boostorg/hof/pull/223#issuecomment-1379492880  

Alright, I updated the script to skip code fences.

---

## Comment 7

> Username: sdarwin  
> Created_at: 2023-01-12 00:27:33 UTC  
> Url: https://github.com/boostorg/hof/pull/223#issuecomment-1379654513  

Superseded by PR 224. Closing this one.

---
