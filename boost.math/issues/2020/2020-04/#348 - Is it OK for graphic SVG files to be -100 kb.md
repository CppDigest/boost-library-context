# #348 - Is it OK for graphic SVG files to be >100 kb? [Closed]

> Username: pabristow  
> Created at: 2020-04-29 11:26:40 UTC  
> Updated at: 2020-04-29 14:25:03 UTC  
> Closed at: 2020-04-29 14:25:03 UTC  
> Url: https://github.com/boostorg/math/issues/348  

@NAThompson has produced some useful and informative ulps_plots, and it would be useful to see more of these plots for other functions.  See /math/doc/graph/*.svg  
  
However these plots are an order of magnitude bigger than previous graphics.  
  
There are several reasons for this, but a little investigation suggests that the number of points plotted (10000) is the main cause.    (There are smaller potential gains from reducing the precision to 3 and from using grouping better to avoid repeating point color and size, etc ). Although using lots of points is visually nicer, IMO reducing to <=1000 points carries the same information with a size of nearer to 50 kb per graph..  
  
Bearing in mind that several large ulps_plots adds some megabytes to the docs that go out with every release, and that this is then packaged up by all the Linux distributions, and installed on perhaps millions of machines worldwide, it adds up to more disk space on a lot of end users machines.  (Because SVG files are so compressible (typically > tenfold), the bandwidth for transmission is less of a worry.).  
  
Are we happy with this use of global resources?  
  
@jzmaddock   
  
(Aside: if only browsers had implemented the defined standard for rendering SVG compressed files, neither bandwidth nor storage size would be an issue.  Inkscape, for example will squeeze these files to svgz with a tenfold reduction).

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-04-29 11:53:58 UTC  
> Updated at: 2020-04-29 11:54:55 UTC  
> Url: https://github.com/boostorg/math/issues/348#issuecomment-621154409  

Did I commit any of those? If I did, my apologies; I need to redo them.  
  
I chose 10,000 points as a default, because it's perfectly fine locally, and then I normally use ~1,000 for docs. Should we change the default?  
  
As to the coloring: I previously did it with css, but after supporting comparisons of multiple implementations, that didn't work . . .   
  
I think only Chrome will render the .svgz; do any other browsers?

---

## Comment 2

> Username: pabristow  
> Created at: 2020-04-29 13:54:58 UTC  
> Url: https://github.com/boostorg/math/issues/348#issuecomment-621224800  

I'm looking at develop and unless I'm confused again the gegenbauers, airy, b_splin etc are showing ~350 kb dated 10Feb2020.  
  
I can't get any of the browsers to work with an Inkscape generated svgz. so that seems to be a non-starter.    
  
But 50 kb-ish is fine and 1000 points look OK (though they take a long time with multiprecision!).

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-04-29 14:01:42 UTC  
> Url: https://github.com/boostorg/math/issues/348#issuecomment-621229956  

Ok, do you want the default changed?

---

## Comment 4

> Username: pabristow  
> Created at: 2020-04-29 14:14:45 UTC  
> Url: https://github.com/boostorg/math/issues/348#issuecomment-621238793  

1000 points looks convincing enough to me to pick out the 'dodgy' and/or difficult zones.

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-04-29 14:25:03 UTC  
> Url: https://github.com/boostorg/math/issues/348#issuecomment-621245329  

@pabristow : [Done](https://github.com/boostorg/math/commit/cd1fe84134f689b957d7258ed32591bfb5c1aaec).
