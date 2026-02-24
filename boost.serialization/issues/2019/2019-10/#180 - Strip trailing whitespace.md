# #180 - Strip trailing whitespace [Closed]

> Username: HDembinski  
> Created at: 2019-10-20 10:05:10 UTC  
> Updated at: 2019-10-21 23:49:11 UTC  
> Closed at: 2019-10-21 23:49:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/180  

Many headers have trailing white space. This makes writing patches difficult, because my editor automatically strips trailing white space.  
  
Would it be possible to strip the white space once for all headers?

---

## Comment 1

> Username: robertramey  
> Created at: 2019-10-20 12:14:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/180#issuecomment-544246750  

hmm how much white space are we talking about? one or two lines? or ....?  I'm generally not a fan of being overly picky about trivial things, though I consider myself pretty picky.  I don't know how this creates a problem

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-10-20 20:51:12 UTC  
> Updated at: 2019-10-20 20:51:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/180#issuecomment-544291327  

I am just talking about so-called trailing white space. The line formatting remains exactly the same, visually there will be no difference, but many lines have extra spaces at the end. These extra  spaces do not contribute to the visual style of the code and can be removed.  
  
I made a PR #181 with all the trailing white-space stripped, but it changes almost every file.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-10-20 20:59:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/180#issuecomment-544292043  

I personally don't care so much about invisible white space either, but some people care a lot about it, see  
https://softwareengineering.stackexchange.com/questions/121555/why-is-trailing-whitespace-a-big-deal  
  
For me personally, it is mostly Linus' fault. He made it so that the diff command in git shows trailing white space as annoying fat red blocks, which look very irritating. Atom, the editor which I use, comes with a built-in functionality to strip trailing white space on save.  
  
In other words, these white space strippers become standard and automatically strip white space from files in Boost.Serialization that I edit. Having to remove all the white space edits from the patch again is extra work.

---

## Comment 4

> Username: robertramey  
> Created at: 2019-10-20 21:04:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/180#issuecomment-544292456  

OK - right now I'm the middle of the first of two very subtle patches.  I was going to tell you to wait.  But now I'm thinking about you'll then have to eliminate the spaces again after I upload.  So the best will be for me to merge the PR right here and then pull the changes.  I'll then re-run my tests.  It just turns out that I'm working on serialization as we speak otherwise it would be a big delay.  Of course I don't have to tell you that if I pull and rerun my tests and something is broken, I'm going to be really, really pissed.  Just consider this.
