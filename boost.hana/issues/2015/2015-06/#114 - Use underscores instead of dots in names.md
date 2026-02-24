# #114 - Use underscores instead of dots in names [Closed]

> Username: ldionne  
> Created at: 2015-06-14 16:58:58 UTC  
> Updated at: 2015-06-28 09:47:04 UTC  
> Closed at: 2015-06-28 09:47:04 UTC  
> Url: https://github.com/boostorg/hana/issues/114  

The following names must be changed:  
- [x] `zip.with` -> `zip_with`  
- [x] `monadic_fold.left` -> `monadic_fold_left`  
- [x] `monadic_fold.right` -> `monadic_fold_right`  
- [x] `scan.left` -> `scan_left`  
- [x] `scan.right` -> `scan_right`  
- [x] `take.at_most`-> `take`  
- [x] `take.exactly` -> `take_exactly`  
- [x] `drop.at_most` -> `drop`  
- [x] `drop.exactly` -> `drop_exactly`  
- [x] `unfold<S>.right` -> `unfold_right<S>`  
- [x] `unfold<S>.left` -> `unfold_left<S>`  
  
This is a generalization of #106.  
Was also asked on the Boost.Devel mailing list by @pfultz2, [here](http://article.gmane.org/gmane.comp.lib.boost.devel/261160).

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-14 21:01:46 UTC  
> Url: https://github.com/boostorg/hana/issues/114#issuecomment-111875867  

I'm unsure what should happen with `less.than`, `equal.to`, `minimum.by` & al., since they are not different algorithms but just syntactic sugar.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-28 09:47:04 UTC  
> Url: https://github.com/boostorg/hana/issues/114#issuecomment-116249129  

Closing this, since I think everything has been addressed.
