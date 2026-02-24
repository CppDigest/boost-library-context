# #703 docs: update gradient tutorial [Merged]

> Username: marco-langer  
> Created at: 2022-06-29 18:04:57 UTC  
> Updated at: 2022-07-04 18:55:50 UTC  
> Merged at: 2022-07-02 15:06:05 UTC  
> Closed at: 2022-07-02 15:06:05 UTC  
> Url: https://github.com/boostorg/gil/pull/703  

### Description  
  
This PR updates the gradient tutorial with currently used style (east-const, trailing return types, alias declarations) and fixes some other minor issues.  
  
Currently a draft, I will update it in the next days.   
  
### Tasklist  
- [ ] Review and approve

---

## Comment 1

> Username: marco-langer  
> Created_at: 2022-07-02 09:11:53 UTC  
> Updated_at: 2022-07-02 09:12:43 UTC  
> Url: https://github.com/boostorg/gil/pull/703#issuecomment-1172866198  

PR is ready for review.  
  
Some of the code snippets didn't compile anymore, because the referenced functions either moved into the detail namespace (like `channel_convert_to_unsigned`) or did not exist anymore at all (like in the mandelbrot example).  
I replaced the mandelbrot snippet with the mandelbrot example from the example directory and changed the tutorial accordingly to calculate the gradient of the luminosity of a rgb8 image rather than the gradient of a grayscale image.  
  
Now all code snippets compile without warnings.

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-07-02 15:05:49 UTC  
> Url: https://github.com/boostorg/gil/pull/703#issuecomment-1172913195  

> Some of the code snippets didn't compile anymore  
  
Maintenance of the documentation snippets is a bit of PITA.  
Some Boost projects switched to AsciiDoc and link snippets from `.cpp` files which are compile-tested by CI jobs,  
For example, see Boost.Build's [example/hello](https://github.com/boostorg/build/tree/f9d4d984f79e5963b2e222eb3dc06ba30d54a8ed/example/hello) and its `readme.adoc`.  
  
I'm not sure if similar is feasible using reStructuredText, or we should switch over to AsciiDoc ;)  
  
> Now all code snippets compile without warnings.  
  
Sweet, thank you!

---

## Comment 3

> Username: marco-langer  
> Created_at: 2022-07-02 15:22:57 UTC  
> Url: https://github.com/boostorg/gil/pull/703#issuecomment-1172915455  

Regarding this `channel_convert_to_unsigned`, which is now in the detail namespace, but also still referenced in this tutorial:   
  
Maybe one should think to pull it out of the detail namespace again, if it is that useful for client code? I don't know the rational why it was moved into the detail namespace at all (this was done 15 years ago!).

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-07-04 18:55:50 UTC  
> Url: https://github.com/boostorg/gil/pull/703#issuecomment-1174084446  

Good catch @marco-langer I have created a new issue #706 to address your questions in future.  
Meanwhile, let's keep it in the `detail` - it is easy to move a thing out of `detail` but the other way around is much harder :)

---
