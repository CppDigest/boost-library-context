# #176 Autodiff - New automatic differentiation library. [Merged]

> Username: pulver  
> Created at: 2019-01-13 21:16:43 UTC  
> Updated at: 2019-05-01 21:32:54 UTC  
> Merged at: 2019-04-25 19:07:58 UTC  
> Closed at: 2019-04-25 19:07:58 UTC  
> Url: https://github.com/boostorg/math/pull/176  

https://github.com/pulver/autodiff/issues/1#issuecomment-450743301

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2019-01-14 19:06:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/176#pullrequestreview-192288220  

📁 doc/differentiation/autodiff.qbk

```diff
  15 |+     #include <boost/math/differentiation/autodiff.hpp>
  16 |+     
  17 |+     namespace boost { namespace math { namespace differentiation { namespace autodiff {
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:10:12 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247598481  

Looks like the docs namespacing still has the autodiff namespace.

> Username: pulver  
> Created_at: 2019-01-14 19:29:03 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247626243  

Indeed, I thought this was consistent with both [your](https://github.com/pulver/autodiff/issues/1#issuecomment-452408620) and [John](https://github.com/pulver/autodiff/issues/1#issuecomment-452416487)'s suggestions. Please correct if I have misinterpretted one of them.

> Username: NAThompson  
> Created_at: 2019-01-14 20:49:08 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247652709  

I think getting rid of the autodiff namespace is the way to go.

> Username: pulver  
> Created_at: 2019-01-15 16:26:15 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247964002  

Autodiff variables will be declared in this way:  
  
    boost::math::differentiation::autodiff<double,3> x(2.0);  
  
and all ancillary structs and functions, included overloaded library functions (cos(), pow(), etc.) will be placed into the namespace `boost::math::differentiation::detail`. Agreed?

> Username: NAThompson  
> Created_at: 2019-01-15 16:43:47 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247971688  

I like it!

> Username: pulver  
> Created_at: 2019-02-04 17:42:44 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253570523  

As agreed to below, the interface is:  
  
    using namespace boost::math::differentiation;  
      
    autodiff_fvar<double,3> x; // General data type for constant or variable.  
    autodiff_fvar<double,3> x(2); // Initialize a constant.  
    autodiff_fvar<double,3> x = make_fvar<double,3>(2); // Initialize a variable.

---

📁 doc/differentiation/autodiff.qbk

```diff
  62 |+ Description]
  63 |+ 
  64 |+ Autodiff is a header-only C++ library that facilitates the [@https://en.wikipedia.org/wiki/Automatic_differentiation automatic differentiation] (forward mode) of mathematical functions in single and multiple variables.
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:11:59 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247599068  

Since you're incorporating this into boost.math, I don't think calling this a library is the correct language.

> Username: pulver  
> Created_at: 2019-01-29 18:22:59 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251957134  

> Since you're incorporating this into boost.math, I don't think calling this a library is the correct language.  
  
Can you suggest an alternative? I've been thinking about this and nothing more appropriate comes to mind. API? C++ header files?  
  
IMO I think calling it a library is appropriate. Compare to the first sentence in the [Statistical Functions and Distributions](https://www.boost.org/doc/libs/1_69_0/libs/math/doc/html/math_toolkit/stat_tut/overview/headers.html) "library" documentation:  
  
> All the code in this library is inside namespace boost::math.  
  
This is in reference to the sub-library of just these functions, which itself is a library.

> Username: NAThompson  
> Created_at: 2019-01-29 18:39:04 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251963256  

It's a nitpick; let's leave it as is.

---

📁 doc/differentiation/autodiff.qbk

```diff
  71 |+ The essential idea of autodiff is the substitution of numbers with polynomials in the evaluation of [autodiff_equation form_0.png]. By selecting the proper polynomial [autodiff_equation form_3.png] as input, the resulting polynomial contains the function's derivatives within the polynomial coefficients. One then multiplies by a factorial term to obtain the desired derivative of any order.
  72 |+ 
  73 |+ Assume one is interested in the first [autodiff_equation form_4.png] derivatives of [autodiff_equation form_0.png] at [autodiff_equation form_1.png]. Then without any loss of precision to the calculation of the derivatives, all terms [autodiff_equation form_5.png] that include powers of [autodiff_equation form_6.png] greater than [autodiff_equation form_4.png] can be discarded, and under these truncation rules, [autodiff_equation form_0.png] provides a polynomial-to-polynomial transformation:
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:17:40 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247600975  

The pngs are rendering out of line. I would use instead:  
  
Assume one is interested in the first /N/ derivatives of /f/ at /x/[sub 0].  
  
Also, start each sentence on a new line.

> Username: jzmaddock  
> Created_at: 2019-01-14 19:14:42 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247621253  

If the equations are trivial, then +1 for writing them in markup rather than png's.  
Otherwise how are they authored - Tex?  Can you generate SVG's for those - I don't recall if that's possible from Tex or not?

> Username: NAThompson  
> Created_at: 2019-01-14 19:18:02 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247622400  

MathJax generates SVGs; I've explicitly extracted it out of the DOM before, but it ain't pretty.

> Username: pulver  
> Created_at: 2019-01-14 20:06:33 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247639444  

I really want to include the mathematics in the documentation. In response to a later [question](https://github.com/boostorg/math/pull/176#discussion_r247605420) about just referring to a reference, I derived the mathematics independently. For instance I think the wikipedia entry on AD is too confusing and obscures the explanation using the chain rule. Focusing on polynomial arithmetic as it relates to the Taylor series is the simplest way to explain this IMHO. Thus I wanted to have the source documentation be in MathJax, which makes doxygen a natural choice. I wrote a separate script (html2qkb.rb) that translated the html doxygen output into QuickBook, and the png images that result are presented nicely when I am viewing them. Here is a screenshot:  
  
![image](https://user-images.githubusercontent.com/39707/51137692-266f9880-180d-11e9-9f41-f615e450a845.png)  
  
I guess you're saying that it's not going to look as nicely for all users?  
  
I am hesitant to make autodiff.qbk the primary source of documentation, as it doesn't handle equations well. To do so would be quite a loss, IMO. Hope this makes sense, and perhaps an alternate suggestion can be made that takes this into consideration, if possible.

> Username: NAThompson  
> Created_at: 2019-01-14 20:28:13 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247646424  

This is Firefox:  
  
<img width="1265" alt="screen shot 2019-01-14 at 1 24 24 pm" src="https://user-images.githubusercontent.com/5459618/51139048-0ab1c580-1800-11e9-9b2d-62b06c534ef3.png">  
  
I'd definitely say keep the display-style equations as SVG, but the inline are not being rendered properly. But it will be very hard to maintain a pile of .pngs whose contents are just an italic letter.

> Username: NAThompson  
> Created_at: 2019-01-14 20:29:29 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247646805  

In terms of references, what do you think is a good one to refer readers to? I'd still prefer to see at least one reference in the docs.

> Username: pulver  
> Created_at: 2019-01-14 21:20:57 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247662971  

I am able to reproduce the out-of-line pngs if the /doc/src/boostbook.css is not included. This can happen if an http server is started in a subdirectory of /libs/math. However if the root of the web service is the root boost directory, then the inclusion of this file will allow the following css rule to be applied to the inline png images  
  
    span.inlinemediaobject img  
    {  
        vertical-align: middle;  
    }  
  
which fixes the vertical alignment issue you are seeing. I imagine this is the way people will generally read the documentation.

> Username: pulver  
> Created_at: 2019-01-14 21:30:48 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247666239  

> I'd definitely say keep the display-style equations as SVG  
  
What's wrong with PNG, as you originally suggested? I'd be happy to if there was an easy way that doxygen could output SVG equations. I did look into that originally and didn't see an easy way, but I can try again if I could see the motivation.  
  
> But it will be very hard to maintain a pile of .pngs whose contents are just an italic letter.  
  
Will it help if I open-source the doxygen html-to-qkb converter?  
  
Sorry this is not ideal, but it is also not ideal to use a format like QuickBook for a mathematics library that doesn't support rendering mathematics.

> Username: NAThompson  
> Created_at: 2019-01-14 21:38:10 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247668453  

The display-style equations as PNG are ok. But I think the single character pngs are a step too far.

> Username: pulver  
> Created_at: 2019-01-14 21:53:49 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247673571  

> In terms of references, what do you think is a good one to refer readers to? I'd still prefer to see at least one reference in the docs.  
  
My primary/only source was the [wikipedia](https://en.wikipedia.org/wiki/Automatic_differentiation) page. I'll add that as a reference.  
  
> I think the single character pngs are a step too far.  
  
I have certainly considered this. For example, this is precisely what I did on the README page on the front of https://github.com/pulver/autodiff since that web page does not have the benefit of the boostbook.css file to fix the vertical alignment of the inline images. So in this case, the italiced versions are preferred. The drawback to do this with the autodiff.qbk file however is 2-fold:  
  
1. It requires manual edits to the output of html2qbk.rb file. This would be repeated each time significant changes were made to the documentation.  
2. It looks worse than the above screenshot.  
  
It would help me to see your perspective if you could give a use-case/scenario that would make it worth these two drawbacks.

> Username: NAThompson  
> Created_at: 2019-01-14 22:00:04 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247675586  

Once the file goes into boost, the .qbk needs to be the primary point of authority for the documentation, since John has to maintain it for years. So the custom tool `html2.qbk.rb` cannot be part of the system.

> Username: jzmaddock  
> Created_at: 2019-01-15 09:03:01 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247807865  

+1 to that: for better or worse we're using quickbook, and we need to keep all the Math lib docs in the one format otherwise it becomes unmaintainable down the road.  
  
We do desperately need a better way to handle equations though...  I do notice that there are online tools that seem to convert LaTex to svg really rather well: http://www.tlhiv.org/ltxpreview/.  But of course it's clunky to have to switch to a separate tool just for equation authoring.  There are a bunch of suggestions for batch/command line conversions here: https://askubuntu.com/questions/33196/how-to-convert-latex-equations-to-svg and another more recent script here: https://www.npmjs.com/package/tex-equation-to-svg.  
  
Assuming they work OK, one quick-ish solution would be to have a quickbook template:  
  
[LaTex filename LaTex goes here^2]  
  
which would simply expand to:  
  
[$filename.svg]  
  
And then run a short script over the quickbook files to extract and write the .svg's  
  
Thoughts?

> Username: NAThompson  
> Created_at: 2019-01-15 16:15:57 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247959410  

My preference would be to keep the svgs in raw form in the repo, rather than generating them, since that'd add another compile step. In addition, I don't feel bad about editing svgs by hand as typos get found, so one of the primary reasons for having a script generate the svgs on the fly, at least for me, is not that important.

> Username: pulver  
> Created_at: 2019-01-15 16:22:52 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247962403  

How's this: The boost autodiff.qbk documentation will be signicantly trimmed down to focus on C++ usage, with a minimal amount of mathematics. I'll keep the mathematical intro, which will involve two SVG equations to provide the basic outline of the theory. All other inline math symbols will be italicezed as Nick suggests. A [link](http://www.unitytechgroup.com/doc/autodiff/) to my more in-depth documentation will be provided which uses MathJax for the notation.

> Username: NAThompson  
> Created_at: 2019-01-15 16:39:04 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247969578  

@pulver : I think this is a good solution. Another very good option for you is since you believe the Wikipedia to be incomplete, is just for you to edit wikipedia. I will be doing this anyway once your feature lands (otherwise no one seems to find what we have), so it will be nice to have an internally consistent wiki page.

> Username: pulver  
> Created_at: 2019-02-04 17:50:23 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253573289  

There are now 7 svg equations in `autodiff.qbk` and the minor inline symbols are non-images as suggested.  
  
Also the external link to more in-depth documentation is replaced with a link to an autodiff.pdf that comes with the library, as John suggested below. (I'll continue to improve this documentation, but I'm going to very quickly have less time to work on this project so I hope we can push out what is here and make improvements to the documentation in the next/later versions.)

---

📁 doc/differentiation/autodiff.qbk

```diff
  76 |+ 
  77 |+ 
  78 |+ C++ includes the ability to overload operators and functions, and thus when [autodiff_equation form_0.png] is written as a template function that can receive and return a generic type, then that is sufficient to perform automatic differentiation: Create a class that models polynomials, and overload all of the arithmetic operators to model polynomial arithmetic that drop all terms in [autodiff_equation form_5.png]. The derivatives are then found in the coefficients of the return value. This is essentially what the autodiff library does (generalizing to multiple independent variables.)
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:18:40 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247601306  

Again, use inline italics (via, say /f/) and put each sentence on a new line.

---

📁 doc/differentiation/autodiff.qbk

```diff
  86 |+ # Mathematical functions, whose derivatives are desired, should accept generic types (template variables) for the parameters that derivatives are calculated with respect to, and internal function calls should allow for [@https://en.cppreference.com/w/cpp/language/adl argument-dependent lookup] (ADL) on these parameters.
  87 |+ 
  88 |+ [h2:quick-install
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:20:59 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247602152  

I would get rid of these installation instructions. Download instructions for boost are given on the boost homepage.

---

📁 doc/differentiation/autodiff.qbk

```diff
 210 |+ [h3 Using the standard Black-Scholes model for pricing European options, calculate call/put prices and greeks.]
 211 |+ 
 212 |+ [@https://en.wikipedia.org/wiki/Greeks_(finance)#Formulas_for_European_option_Greeks https://en.wikipedia.org/wiki/Greeks_(finance)#Formulas_for_European_option_Greeks]
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:23:34 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247603205  

It's a bit ugly to use a link to an url. I would use something like:  
  
Calculating [@https://en.wikipedia.org/wiki/Greeks_(finance)#Formulas_for_European_option_Greeks Greeks]

> Username: pulver  
> Created_at: 2019-02-04 18:01:44 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253577703  

This example has been removed.

---

📁 doc/differentiation/autodiff.qbk

```diff
 391 |+     autodiff put  ultima = -0.0922426864775683
 392 |+           formula ultima = -0.0922426864775685
 393 |+     **/
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:26:08 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247604146  

This is too much detail. In essence, this example demonstrates the accuracy of your method. But the reader will believe that you have done your due diligence on accuracy, and will refer to the docs to find a maximally legible example to understand the code.

> Username: pulver  
> Created_at: 2019-02-04 18:02:05 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253577828  

This example has been removed.

---

📁 doc/differentiation/autodiff.qbk

```diff
 426 |+     }
 427 |+     
 428 |+     // Derivatives calculated from symbolic differentiation by Mathematica for comparison.
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:27:22 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247604544  

Again, this is too much detail. People will believe that you've done this right! Give them the minimal amount of code that shows how to calculate a mixed partial.

---

📁 doc/differentiation/autodiff.qbk

```diff
 513 |+ Mathematics]
 514 |+ 
 515 |+ In order for the usage of the autodiff library to make sense, a basic understanding of the mathematics will help.
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:29:35 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247605420  

It may be better to refer to a reference than attempt to explain the theory here. Certainly a reference your followed should be added, even if you don't remove this.

---

📁 doc/differentiation/autodiff.qbk

```diff
 585 |+ Given polynomials [autodiff_equation form_46.png] and [autodiff_equation form_49.png], how is [autodiff_equation form_50.png] calculated?
 586 |+ 
 587 |+ To answer this, one simply expands [autodiff_equation form_46.png] and [autodiff_equation form_49.png] into their polynomial forms and add them together:
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:34:41 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247607090  

I admit to a particular antipathy to these inline png equations, because I use Dark Reader, and the pngs don't invert like the rest of the text.

> Username: pulver  
> Created_at: 2019-01-14 21:58:19 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247675052  

Perhaps I should make them with a white background instead of transparent?

> Username: NAThompson  
> Created_at: 2019-01-14 22:03:14 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247676589  

The background is good, it's the text itself staying black on a black background that is a problem.


📁 example/autodiff_black_scholes.cpp

```diff
  72 |+   const double formula_color =
  73 |+     static_cast<double>(-phi(d1)/(2*S*tau*sigma*sqrt(tau))*(1+(2*r*tau-d2*sigma*sqrt(tau))*d1/(sigma*sqrt(tau))));
  74 |+   const double formula_ultima = -formula_vega*static_cast<double>((d1*d2*(1-d1*d2)+d1*d1+d2*d2)/(sigma*sigma));
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:36:58 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247607843  

Do all of these variables need to be const? If not, it might be good to remove them for maximum readability.

> Username: pulver  
> Created_at: 2019-02-04 18:02:48 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253578153  

Left in example, but removed from documentation.

---

📁 example/autodiff_black_scholes.cpp

```diff
   1 |+ //           Copyright Matthew Pulver 2018 - 2019.
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:37:57 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247608172  

I don't believe that you added this example to `example/Jamfile.v2`.

> Username: pulver  
> Created_at: 2019-02-04 18:03:10 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253578286  

Added.


📁 example/autodiff_fourth_power.cpp

```diff
   1 |+ //           Copyright Matthew Pulver 2018 - 2019.
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:42:14 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247609628  

This file is also not included in the `example/Jamfile.v2`.

> Username: pulver  
> Created_at: 2019-02-04 18:03:21 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253578355  

Added.


📁 example/autodiff_mixed_partials.cpp

```diff
  89 |+           max_relative_error = std::max(error, max_relative_error);
  90 |+         }
  91 |+   std::cout << "max_relative_error = " << std::setprecision(3) << max_relative_error
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:46:02 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247611080  

Could you explicitly bracket this cout/for loops? It's not obvious which for loop it should be in.

> Username: pulver  
> Created_at: 2019-02-04 18:06:09 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253579367  

Sorry I missed updating this one, but it has been removed from documentation.


📁 include/boost/math/differentiation/autodiff.hpp

```diff
   4 |+ //           https://www.boost.org/LICENSE_1_0.txt)
   5 |+ 
   6 |+ /*!
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:47:12 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247611489  

So it's probably best to remove this and keep all the documentation in the .qbk file.

> Username: pulver  
> Created_at: 2019-02-04 18:06:19 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253579425  

Done.

---

📁 include/boost/math/differentiation/autodiff.hpp

```diff
 587 |+ 
 588 |+ // Automatic Differentiation v1
 589 |+ namespace boost { namespace math { namespace differentiation { namespace autodiff { inline namespace v1 {
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:48:43 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247612049  

What was @jzmaddock's opinion on the autodiff namespace? I think I recall him wanting the namespaces to match the directory structure.

---

📁 include/boost/math/differentiation/autodiff.hpp

```diff
 783 |+ 
 784 |+ // C++11 Compatibility
 785 |+ #ifdef BOOST_NO_CXX17_IF_CONSTEXPR
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:51:59 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247613235  

Is supporting both C++11 and C++17 a good idea? It seems like it'll become a source of uninteresting bugs and a maintenance burden. I'll defer to @jzmaddock's opinion on this, but my preference is that it be either written in C++11, or C++17, not both.

> Username: pulver  
> Created_at: 2019-01-15 17:14:49 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247984035  

I was originally afraid of that as well but a volunteer from the mailing list did some pretty clean C++11 compatibility integration, without having to use SFINAE. Also since I'll be moving things into `boost::math::differentiation::detail` I can replace the static constexpr class methods which were giving MSVC2015 some problems, which should make it "more" C++11 compatible. I would also hate to have to undo his contribution. I think the C++11 additions/alternative are pretty straight-forward.

> Username: pulver  
> Created_at: 2019-02-04 18:09:13 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253580418  

Done. Compiles now with MSVC 2015. https://ci.appveyor.com/project/pulver/autodiff/builds/22098322

---

📁 include/boost/math/differentiation/autodiff.hpp

```diff
1110 |+         retval.v[i] = v[i] - cr.v[i];
1111 |+     if BOOST_AUTODIFF_IF_CONSTEXPR (Order < Order2)
1112 |+         for (size_t i=Order+1 ; i<=Order2 ; ++i)
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:53:37 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247613839  

I would definitely prefer explicit braces here.

> Username: pulver  
> Created_at: 2019-02-04 18:13:51 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253581947  

If I add it here, for consistency they will have to be added to tens/hundreds of other places. In isolation I would tend to agree with you, but in this case there is a certain overall consistency to this particular project that I think makes sense.

---

📁 include/boost/math/differentiation/autodiff.hpp

```diff
1426 |+     static_assert(sizeof...(orders) <= depth(),
1427 |+         "Number of parameters to derivative(...) cannot exceed the number of dimensions in the dimension<...>.");
1428 |+     return at(orders...) * (... * boost::math::factorial<root_type>(ord
```

> Username: NAThompson  
> Created_at: 2019-01-14 18:59:07 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247615868  

Can this be changed to `using std::sqrt; return sqrt(cr)`? It should be much faster.

> Username: pulver  
> Created_at: 2019-02-04 18:18:51 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253583679  

It's not that simple. When a math function is overloaded into the autodiff library, it must accurately compute all higher order derivatives, as that is the expectation of the overloaded function. Here it is: https://github.com/boostorg/math/blob/7748f2ea19f270a72ec7ab9004a33832e8c01c52/include/boost/math/differentiation/autodiff.hpp#L1161

---

📁 include/boost/math/differentiation/autodiff.hpp

```diff
1426 |+     static_assert(sizeof...(orders) <= depth(),
1427 |+         "Number of parameters to derivative(...) cannot exceed the number of dimensions in the dimension<...>.");
1428 |+     return at(orders...) * (... * boost::math::factorial<root_type>(ord
```

> Username: NAThompson  
> Created_at: 2019-01-14 19:01:24 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247616801  

Is it idiomatic to set the precision on the ostream in a call? Presumably people want to set their own precision.

> Username: pulver  
> Created_at: 2019-02-04 18:19:24 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253583862  

Agreed; setting the precision has been removed.

---

📁 include/boost/math/differentiation/autodiff.hpp

```diff
1426 |+     static_assert(sizeof...(orders) <= depth(),
1427 |+         "Number of parameters to derivative(...) cannot exceed the number of dimensions in the dimension<...>.");
1428 |+     return at(orders...) * (... * boost::math::factorial<root_type>(ord
```

> Username: NAThompson  
> Created_at: 2019-01-14 19:01:57 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247617004  

By the way, why the `v1` namespace?

> Username: jzmaddock  
> Created_at: 2019-01-14 19:11:26 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247620217  

Library versioning?

> Username: pulver  
> Created_at: 2019-01-14 22:02:39 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247676414  

I was just following these instructions:  
  
[COUPLING: Strongly consider versioning your library's namespace using inline namespaces](https://svn.boost.org/trac10/wiki/BestPracticeHandbook#a2.COUPLING:Stronglyconsiderversioningyourlibrarysnamespaceusinginlinenamespacesandrequestinguserstoaliasaversionednamespaceinsteadofusingitdirectly)

> Username: pulver  
> Created_at: 2019-02-04 18:21:23 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253584663  

Replaced `inline namespace v1` with `inline namespace autodiff_v1`. (This is invisible to general users. It's only when there is an upgrade to v2 that fundamentally changes the API, and someone wants to use the old version, then they will modify their code to explicitly use this namespace.)


📁 test/test_autodiff.cpp

```diff
 109 |+     constexpr int m = 3;
 110 |+     constexpr int n = 4;
 111 |+     constexpr double cx = 10.0;
```

> Username: NAThompson  
> Created_at: 2019-01-14 19:04:43 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247618060  

Generally I template my test cases on a Real type, then test them at float, double, long double, and cpp_bin_float_50 precision.

> Username: pulver  
> Created_at: 2019-01-27 17:56:26 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251247721  

### Ambiguous Overload for 10 Binary Operators  
  
I'm encountering an `error: ambiguous overload for ‘operator+’` when compiling  
```c++  
mp + ad  
```  
where the LHS `mp` is of type `cpp_bin_float<>` and the RHS `ad` is of type `autodiff_fvar<>`. The two competing candidates are:  
* [operator + (const number<B, et_off>& a, const V& b)](https://github.com/boostorg/multiprecision/blob/f3e4cee0ccff02ffbc1b56a06ed693d1029b1d4b/include/boost/multiprecision/detail/no_et_ops.hpp#L53) in boost/multiprecision/detail/no_et_ops.hpp  
* [operator+(const typename fvar<RealType,Order>::root_type& ca, const fvar<RealType,Order>& cr)](https://github.com/pulver/autodiff/blob/85d34700537a530472e8b22970b658dfd49065af/include/boost/math/differentiation/autodiff.hpp#L1098) which is a friend function in autodiff.hpp.  
  
This does not happen for `ad + mp` since that is calling `fvar operator+(const root_type&) const;` which is a member of `fvar`.  
  
This problem exists for all 4 arithmetic operators (+,-,*,/) and all 6 comparison operators (==,!=,<,<=,>,>=).  
  
Interestingly, there is **no problem** when using **`cpp_dec_float<>`** under the same conditions.  
  
Is there a known/recommended way to resolve this? May be related: https://github.com/boostorg/multiprecision/issues/109

> Username: jzmaddock  
> Created_at: 2019-01-27 19:12:27 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251250758  

That's strange - the overloads should be enabled/disabled the same for both types.  
  
In any case the multiprecision ones are only enabled when  
  
```  
template <class T, class Num>  
struct is_compatible_arithmetic_type  
   : public mpl::bool_<  
         is_convertible<T, Num>::value  
         && !is_same<T, Num>::value  
         && !is_number_expression<T>::value>  
{};  
```  
  
evaluates to `true`, which would imply that `autodiff_fvar<>` is implicitly convertible to `cpp_bin_float`, if that's the case then we have a genuine ambiguity because the conversion could go either way?  
  
Specializing `is_compatible_arithmetic_type` would sort things out in short order, but I'd rather understand why there's an ambiguity first.

> Username: pulver  
> Created_at: 2019-01-27 20:32:41 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251253968  

> ... which would imply that `autodiff_fvar<>` is implicitly convertible to `cpp_bin_float` ...  
  
~~Yes, in the class definition there is the [declaration](https://github.com/pulver/autodiff/blob/master/include/boost/math/differentiation/autodiff.hpp#L758)~~  
```  
explicit operator root_type() const;  
```  
  
Sorry, that's explicitly not implicit :)  
  
I'm getting two different results for cpp_bin_float and cpp_dec_float when attempting implicit conversions.  
  
For cpp_dec_float_50, the following is CORRECTLY giving a compiler error when attempting an invalid implicit conversion from autodiff to cpp_dec_float_50:  
```c++  
using cpp_dec_float_50 = boost::multiprecision::cpp_dec_float_50;  
autodiff_fvar<cpp_dec_float_50,3> ad_dec = 10;  
cpp_dec_float_50 mp_dec = ad_dec; // error: cannot convert  
```  
  
However the error messages when doing the same for cpp_bin_float_50 are different and much more verbose:  
```c++  
using cpp_bin_float_50 = boost::multiprecision::cpp_bin_float_50;  
autodiff_fvar<cpp_bin_float_50,3> ad_bin = 10;  
cpp_bin_float_50 mp_bin = ad_bin; // line 22  
```  
produces:  
```  
In file included from /usr/include/boost/multiprecision/traits/is_variable_precision.hpp:10:0,  
                 from /usr/include/boost/multiprecision/detail/precision.hpp:9,  
                 from /usr/include/boost/multiprecision/number.hpp:23,  
                 from /usr/include/boost/multiprecision/cpp_int.hpp:12,  
                 from /usr/include/boost/multiprecision/cpp_bin_float.hpp:9,  
                 from conversion.cpp:2:  
/usr/include/boost/multiprecision/detail/number_base.hpp: In instantiation of ‘struct boost::multiprecision::detail::canonical_imp<int, boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>, mpl_::int_<0> >’:  
/usr/include/boost/multiprecision/detail/number_base.hpp:267:65:   required from ‘struct boost::multiprecision::detail::canonical<int, boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3> >’  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:342:83:   required from ‘typename boost::enable_if_c<((boost::multiprecision::number_category<Other>::value == number_kind_floating_point) && (! boost::is_floating_point<Float>::value)), boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>&>::type boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::assign_float(Float) [with Float = boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>; unsigned int Digits = 50; boost::multiprecision::backends::digit_base_type DigitBase = (boost::multiprecision::backends::digit_base_type)10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0; typename boost::enable_if_c<((boost::multiprecision::number_category<Other>::value == number_kind_floating_point) && (! boost::is_floating_point<Float>::value)), boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>&>::type = boost::multiprecision::backends::cpp_bin_float<50>&]’  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:113:7:   required from ‘boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const Float&, const typename boost::enable_if_c<(((((boost::multiprecision::number_category<Other>::value == number_kind_floating_point) && (std::numeric_limits<Type>::digits <= (int)(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count))) && (std::numeric_limits<Type>::radix == 2)) && std::numeric_limits<Type>::is_specialized) && (! boost::is_same<V, __float128>::value))>::type*) [with Float = boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>; unsigned int Digits = 50; boost::multiprecision::backends::digit_base_type DigitBase = (boost::multiprecision::backends::digit_base_type)10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0; typename boost::enable_if_c<(((((boost::multiprecision::number_category<Other>::value == number_kind_floating_point) && (std::numeric_limits<Type>::digits <= (int)(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count))) && (std::numeric_limits<Type>::radix == 2)) && std::numeric_limits<Type>::is_specialized) && (! boost::is_same<V, __float128>::value))>::type = void]’  
/usr/include/boost/multiprecision/number.hpp:73:37:   required from ‘constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, typename boost::enable_if_c<(boost::is_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value))>::type*) [with V = boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>; Backend = boost::multiprecision::backends::cpp_bin_float<50>; boost::multiprecision::expression_template_option ExpressionTemplates = (boost::multiprecision::expression_template_option)0; typename boost::enable_if_c<(boost::is_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value))>::type = void]’  
conversion.cpp:22:29:   required from here  
/usr/include/boost/multiprecision/detail/number_base.hpp:211:12: error: no type named ‘signed_types’ in ‘class boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’  
    >::type iter_type;  
            ^~~~~~~~~  
/usr/include/boost/multiprecision/detail/number_base.hpp:212:68: error: no type named ‘signed_types’ in ‘class boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’  
    typedef typename mpl::end<typename Backend::signed_types>::type end_type;  
                                                                    ^~~~~~~~  
/usr/include/boost/multiprecision/detail/number_base.hpp:213:121: error: no type named ‘signed_types’ in ‘class boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’  
 edef typename mpl::eval_if<boost::is_same<iter_type, end_type>, mpl::identity<Val>, mpl::deref<iter_type> >::type type;  
                                                                                                                   ^~~~  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp: In instantiation of ‘typename boost::enable_if_c<((boost::multiprecision::number_category<Other>::value == number_kind_floating_point) && (! boost::is_floating_point<Float>::value)), boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>&>::type boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::assign_float(Float) [with Float = boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>; unsigned int Digits = 50; boost::multiprecision::backends::digit_base_type DigitBase = (boost::multiprecision::backends::digit_base_type)10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0; typename boost::enable_if_c<((boost::multiprecision::number_category<Other>::value == number_kind_floating_point) && (! boost::is_floating_point<Float>::value)), boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>&>::type = boost::multiprecision::backends::cpp_bin_float<50>&]’:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:113:7:   required from ‘boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const Float&, const typename boost::enable_if_c<(((((boost::multiprecision::number_category<Other>::value == number_kind_floating_point) && (std::numeric_limits<Type>::digits <= (int)(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count))) && (std::numeric_limits<Type>::radix == 2)) && std::numeric_limits<Type>::is_specialized) && (! boost::is_same<V, __float128>::value))>::type*) [with Float = boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>; unsigned int Digits = 50; boost::multiprecision::backends::digit_base_type DigitBase = (boost::multiprecision::backends::digit_base_type)10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0; typename boost::enable_if_c<(((((boost::multiprecision::number_category<Other>::value == number_kind_floating_point) && (std::numeric_limits<Type>::digits <= (int)(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count))) && (std::numeric_limits<Type>::radix == 2)) && std::numeric_limits<Type>::is_specialized) && (! boost::is_same<V, __float128>::value))>::type = void]’  
/usr/include/boost/multiprecision/number.hpp:73:37:   required from ‘constexpr boost::multiprecision::number<Backend, ExpressionTemplates>::number(const V&, typename boost::enable_if_c<(boost::is_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value))>::type*) [with V = boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>; Backend = boost::multiprecision::backends::cpp_bin_float<50>; boost::multiprecision::expression_template_option ExpressionTemplates = (boost::multiprecision::expression_template_option)0; typename boost::enable_if_c<(boost::is_convertible<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, Backend>::type, Backend>::value))>::type = void]’  
conversion.cpp:22:29:   required from here  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:345:29: error: no matching function for call to ‘eval_fpclassify(boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>&)’  
       switch(eval_fpclassify(f))  
              ~~~~~~~~~~~~~~~^~~  
In file included from conversion.cpp:3:0:  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2800:12: note: candidate: template<unsigned int Digits10, class ExponentType, class Allocator> int boost::multiprecision::backends::eval_fpclassify(const boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>&)  
 inline int eval_fpclassify(const cpp_dec_float<Digits10, ExponentType, Allocator>& x)  
            ^~~~~~~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2800:12: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:345:29: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘const boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>’  
       switch(eval_fpclassify(f))  
              ~~~~~~~~~~~~~~~^~~  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1587:12: note: candidate: template<unsigned int Digits, boost::multiprecision::backends::digit_base_type DigitBase, class Allocator, class Exponent, Exponent MinE, Exponent MaxE> int boost::multiprecision::backends::eval_fpclassify(const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&)  
 inline int eval_fpclassify(const cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &arg)  
            ^~~~~~~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1587:12: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:345:29: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>’  
       switch(eval_fpclassify(f))  
              ~~~~~~~~~~~~~~~^~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:365:12: error: ‘class boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ has no member named ‘negate’  
          f.negate();  
          ~~^~~~~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:378:17: error: no matching function for call to ‘eval_frexp(boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>&, boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>&, int*)’  
       eval_frexp(f, f, &e);  
       ~~~~~~~~~~^~~~~~~~~~  
In file included from conversion.cpp:3:0:  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2992:63: note: candidate: template<unsigned int Digits10, class ExponentType, class Allocator> typename boost::disable_if<boost::is_same<ExponentType, int> >::type boost::multiprecision::backends::eval_frexp(boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>&, const boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>&, int*)  
 inline typename disable_if<is_same<ExponentType, int> >::type eval_frexp(cpp_dec_float<Digits10, ExponentType, Allocator>& result, const cpp_dec_float<Digits10, ExponentType, Allocator>& x, int* e)  
                                                               ^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2992:63: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:378:17: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>’  
       eval_frexp(f, f, &e);  
       ~~~~~~~~~~^~~~~~~~~~  
In file included from conversion.cpp:3:0:  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2923:13: note: candidate: template<unsigned int Digits10, class ExponentType, class Allocator> void boost::multiprecision::backends::eval_frexp(boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>&, const boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>&, ExponentType*)  
 inline void eval_frexp(cpp_dec_float<Digits10, ExponentType, Allocator>& result, const cpp_dec_float<Digits10, ExponentType, Allocator>& x, ExponentType* e)  
             ^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2923:13: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:378:17: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>’  
       eval_frexp(f, f, &e);  
       ~~~~~~~~~~^~~~~~~~~~  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1505:13: note: candidate: template<unsigned int Digits, boost::multiprecision::backends::digit_base_type DigitBase, class Allocator, class Exponent, Exponent MinE, Exponent MaxE, class I> void boost::multiprecision::backends::eval_frexp(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, I*)  
 inline void eval_frexp(cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &res, const cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &arg, I *pe)  
             ^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1505:13: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:378:17: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>’  
       eval_frexp(f, f, &e);  
       ~~~~~~~~~~^~~~~~~~~~  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1488:13: note: candidate: template<unsigned int Digits, boost::multiprecision::backends::digit_base_type DigitBase, class Allocator, class Exponent, Exponent MinE, Exponent MaxE> void boost::multiprecision::backends::eval_frexp(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, Exponent*)  
 inline void eval_frexp(cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &res, const cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &arg, Exponent *e)  
             ^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1488:13: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:378:17: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>’  
       eval_frexp(f, f, &e);  
       ~~~~~~~~~~^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:381:20: error: no matching function for call to ‘eval_ldexp(boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>&, boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>&, const int&)’  
          eval_ldexp(f, f, bits);  
          ~~~~~~~~~~^~~~~~~~~~~~  
In file included from conversion.cpp:3:0:  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2905:13: note: candidate: template<unsigned int Digits10, class ExponentType, class Allocator, class ArgType> void boost::multiprecision::backends::eval_ldexp(boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>&, const boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>&, ArgType)  
 inline void eval_ldexp(cpp_dec_float<Digits10, ExponentType, Allocator>& result, const cpp_dec_float<Digits10, ExponentType, Allocator>& x, ArgType e)  
             ^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:2905:13: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:381:20: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>’  
          eval_ldexp(f, f, bits);  
          ~~~~~~~~~~^~~~~~~~~~~~  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1556:56: note: candidate: template<unsigned int Digits, boost::multiprecision::backends::digit_base_type DigitBase, class Allocator, class Exponent, Exponent MinE, Exponent MaxE, class I> typename boost::enable_if_c<boost::is_signed<S>::value>::type boost::multiprecision::backends::eval_ldexp(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, I)  
 inline typename enable_if_c<is_signed<I>::value>::type eval_ldexp(cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &res, const cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &arg, I e)  
                                                        ^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1556:56: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:381:20: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>’  
          eval_ldexp(f, f, bits);  
          ~~~~~~~~~~^~~~~~~~~~~~  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1546:58: note: candidate: template<unsigned int Digits, boost::multiprecision::backends::digit_base_type DigitBase, class Allocator, class Exponent, Exponent MinE, Exponent MaxE, class I> typename boost::enable_if_c<boost::is_unsigned<U>::value>::type boost::multiprecision::backends::eval_ldexp(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, I)  
 inline typename enable_if_c<is_unsigned<I>::value>::type eval_ldexp(cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &res, const cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &arg, I e)  
                                                          ^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1546:58: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:381:20: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>’  
          eval_ldexp(f, f, bits);  
          ~~~~~~~~~~^~~~~~~~~~~~  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1517:13: note: candidate: template<unsigned int Digits, boost::multiprecision::backends::digit_base_type DigitBase, class Allocator, class Exponent, Exponent MinE, Exponent MaxE> void boost::multiprecision::backends::eval_ldexp(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, Exponent)  
 inline void eval_ldexp(cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &res, const cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> &arg, Exponent e)  
             ^~~~~~~~~~  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:1517:13: note:   template argument deduction/substitution failed:  
In file included from conversion.cpp:2:0:  
/usr/include/boost/multiprecision/cpp_bin_float.hpp:381:20: note:   ‘boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50> >, 3>’ is not derived from ‘boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>’  
          eval_ldexp(f, f, bits);  
          ~~~~~~~~~~^~~~~~~~~~~~  
```  
This is with boost 1.69.0 and g++ (GCC) 7.3.1 20180303 (Red Hat 7.3.1-5)  
Command line: g++ conversion.cpp

> Username: jzmaddock  
> Created_at: 2019-01-27 21:06:14 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251255244  

OK that's weird, it may take a day or two, but let me look into this.

> Username: pulver  
> Created_at: 2019-01-27 21:26:06 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251255973  

In case this helps, `std::is_convertible` also appears to think `autodiff_fvar<T,3>` is convertible to `T=cpp_bin_float_50` but not to `T=cpp_dec_float_50`:  
  
```c++  
#include <boost/math/differentiation/autodiff.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <iostream>  
#include <type_traits>  
  
int main()  
{  
  using namespace boost::math::differentiation;  
  using cpp_dec_float_50 = boost::multiprecision::cpp_dec_float_50;  
  using cpp_bin_float_50 = boost::multiprecision::cpp_bin_float_50;  
  
  std::cout << "std::is_convertible<autodiff_fvar<double,3>,double>::value = "  
    << std::is_convertible<autodiff_fvar<double,3>,double>::value << std::endl;  
  std::cout << "std::is_convertible<autodiff_fvar<cpp_dec_float_50,3>,cpp_dec_float_50>::value = "  
    << std::is_convertible<autodiff_fvar<cpp_dec_float_50,3>,cpp_dec_float_50>::value << std::endl;  
  std::cout << "std::is_convertible<autodiff_fvar<cpp_bin_float_50,3>,cpp_bin_float_50>::value = "  
    << std::is_convertible<autodiff_fvar<cpp_bin_float_50,3>,cpp_bin_float_50>::value << std::endl;  
  
  return 0;  
}  
```  
  
produces output:  
```  
std::is_convertible<autodiff_fvar<double,3>,double>::value = 0  
std::is_convertible<autodiff_fvar<cpp_dec_float_50,3>,cpp_dec_float_50>::value = 0  
std::is_convertible<autodiff_fvar<cpp_bin_float_50,3>,cpp_bin_float_50>::value = 1  
```  
  
(It's also the same for an older version of the code submitted with this PR, replacing `autodiff_fvar` with `autodiff::variable` above.)

> Username: pulver  
> Created_at: 2019-01-28 21:29:27 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251601030  

### 9/3 != 3 for cpp_dec_float_50  
  
Separate issue, but related to running tests over different numeric types. Compiling and running the following minimal example  
```c++  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <iostream>  
#include <type_traits>  
  
int main()  
{  
    const boost::multiprecision::cpp_dec_float_50 nine = 9;  
    const boost::multiprecision::cpp_dec_float_50 three = 3;  
  
    std::cout.precision(73);  
    std::cout << "nine = " << nine << std::endl;  
    std::cout << "three = " << three << std::endl;  
    std::cout << "nine/three = " << nine/three << std::endl;  
    std::cout << "(nine/three == 3) = " << (nine/three == 3) << std::endl;  
  
    return 0;  
}  
```  
produces output:  
```  
nine = 9  
three = 3  
nine/three = 3.000000000000000000000000000000000000000000000000000000000000000000000001  
(nine/three == 3) = 0  
```  
  
I'm aware of floating point issues such as 1/10 is not exactly equal to 0.1, since 0.1 can't be exactly represented by `float` or `double`. However I do expect that 9/3 should be *exactly* equal to 3 using `float` or `double` since the numerator, denominator, and quotient are all exactly representable, and in fact this is required by the IEEE standard. However this is not required by the boost multiprecision types?  
  
The reason I ask is because I have chosen values to test with autodiff that don't require any non-zero tolerance level. Since derivatives can be calculated exactly, then why not design tests around this fact. I have autodiff tests that are exact for `float`, `double`, and `long double` but are failing for `cpp_dec_float_50` for reasons such as the above.  
  
Perhaps I missed it, but I wasn't able to find discussion of this in the docs or faq.

> Username: jzmaddock  
> Created_at: 2019-01-29 08:48:24 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251737424  

This sucks, but it was part of the inherent design of cpp_dec_float - long story short it calculates the inverse and multiplies for division as this is faster than the alternatives, but doesn't give an exact result without correct rounding, and cpp_dec_float does no rounding.... instead it has "guard digits" rather like GMP's `mpf_t`.   `cpp_bin_float` does do exact rounding though.

> Username: pulver  
> Created_at: 2019-01-29 17:20:38 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r251933499  

> it calculates the inverse and multiplies for division as this is faster than the alternatives, but doesn't give an exact result  
  
I did exactly this with an earlier implementation of `operator/` overload for autodiff. When I found I was getting similar results above, I decided that having accurate division outweighed the slight performance hit. In the end, the improvement in accuracy was noticable; the difference in speed was not.

> Username: pulver  
> Created_at: 2019-02-04 17:59:54 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253577027  

Tests with `cpp_dec_bin` are omitted due to imprecise arithmetic.

---

📁 test/test_autodiff.cpp

```diff
 211 |+     for (int i=0 ; i<=m ; ++i)
 212 |+         for (int j=0 ; j<=n ; ++j)
 213 |+             if (i==0 && j==0)
```

> Username: NAThompson  
> Created_at: 2019-01-14 19:05:25 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247618356  

Again, I personally prefer explicit bracing. Don't know if this is bikeshedding at the point or not.

> Username: jzmaddock  
> Created_at: 2019-01-14 19:12:15 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r247620478  

Normally, yes :)  But for lots of nested loops I would agree with Nick.

> Username: pulver  
> Created_at: 2019-02-04 18:24:43 UTC  
> Updated_at: 2019-02-28 13:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#discussion_r253585922  

Updated for this case, but I think it adds unncessary bloat. In this case, it added 26 extra lines that I don't think help, and instead causes extra scrolling to look through the code.  
  
In isolation I would agree, but numerous tests that all follow this same pattern I find is easier to read without all the braces.


---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-01-14 21:15:36 UTC  
> Updated_at: 2019-01-14 21:21:29 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454164122  

Cool, I just refactored the condition number calculation working with automatic differentiation. It went well, didn't segfault or give any wrong answers, so I'm very happy about that. My only comment is that it seems like the API could be slightly more ergonomic, and some effort on this point would have very high return.  
  
For instance, do you think that a free function would be what people would more naturally write? To wit, instead of  
  
```cpp  
    Real fx = y.derivative(0);  
    Real fp = y.derivative(1);  
```  
  
could we do  
  
```  
    Real fp = derivative(y, 1);  
```  
  
Another very cool application we could do is a Newton's method without requiring the user to write the evaluation and the derivative evaluation.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-01-14 21:50:37 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454174960  

Just checked the speed:  
  
```cpp  
#include <cmath>  
#include <benchmark/benchmark.h>  
#include <boost/math/differentiation/autodiff.hpp>  
  
using std::sin;  
using std::cos;  
using boost::math::differentiation::autodiff::variable;  
  
auto f = [](auto x) { return sin(x)*cos(x)/(x*x*x); };  
  
template<class Real>  
void BM_Autodiff0(benchmark::State& state)  
{  
    variable<Real, 0> x(0.0);  
    for (auto _ : state)  
    {  
        x = x + 0.00001;  
        auto y = f(x);  
        benchmark::DoNotOptimize(y.derivative(0));  
    }  
}  
  
BENCHMARK_TEMPLATE(BM_Autodiff0, double);  
  
  
template<class Real>  
void BM_Autodiff1(benchmark::State& state)  
{  
    variable<Real, 1> x(0.0);  
    for (auto _ : state)  
    {  
        x = x + 0.00001;  
        auto y = f(x);  
        benchmark::DoNotOptimize(y.derivative(1));  
    }  
}  
  
BENCHMARK_TEMPLATE(BM_Autodiff1, double);  
  
template<class Real>  
void BM_Autodiff2(benchmark::State& state)  
{  
    variable<Real, 2> x(0.0);  
    for (auto _ : state)  
    {  
        x = x + 0.00001;  
        auto y = f(x);  
        benchmark::DoNotOptimize(y.derivative(2));  
    }  
}  
  
BENCHMARK_TEMPLATE(BM_Autodiff2, double);  
  
template<class Real>  
void BM_Autodiff3(benchmark::State& state)  
{  
    variable<Real, 3> x(0.0);  
    for (auto _ : state)  
    {  
        x += 0.00001;  
        auto y = f(x);  
        benchmark::DoNotOptimize(y.derivative(3));  
    }  
}  
  
BENCHMARK_TEMPLATE(BM_Autodiff3, double);  
  
template<class Real>  
void BM_Autodiff4(benchmark::State& state)  
{  
    variable<Real, 4> x(0.0);  
    for (auto _ : state)  
    {  
        x += 0.00001;  
        auto y = f(x);  
        benchmark::DoNotOptimize(y.derivative(4));  
    }  
}  
  
BENCHMARK_TEMPLATE(BM_Autodiff4, double);  
  
template<class Real>  
void BM_Autodiff5(benchmark::State& state)  
{  
    variable<Real, 5> x(0.0);  
    for (auto _ : state)  
    {  
        x += 0.00001;  
        auto y = f(x);  
        benchmark::DoNotOptimize(y.derivative(5));  
    }  
}  
  
BENCHMARK_TEMPLATE(BM_Autodiff5, double);  
  
BENCHMARK_MAIN();  
```  
  
Results:  
  
```bash  
------------------------------------------------------------  
Benchmark                     Time           CPU Iterations  
------------------------------------------------------------  
BM_Autodiff0<double>         15 ns         15 ns   42170453  
BM_Autodiff1<double>         55 ns         55 ns   13048746  
BM_Autodiff2<double>         81 ns         81 ns    8352823  
BM_Autodiff3<double>        117 ns        117 ns    6019434  
BM_Autodiff4<double>        179 ns        178 ns    3929913  
BM_Autodiff5<double>        245 ns        243 ns    2853614  
```  
  
To me this is very good. I haven't dived into the ASM to see where the hotspots are, but it appears everything is in order.  
  
BTW, it looks like you're doing checked indexing via the `.at()` member on the `std::array` by default; is this intentional?

---

## Comment 4

> Username: pulver  
> Created_at: 2019-01-14 22:09:45 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454180703  

> it looks like you're doing checked indexing via the .at() member on the std::array by default; is this intentional?  
  
Yes. I thought that the slight speed sacrifice was worth throwing an exception for an out-of-bounds parameter due to accidental mis-use than to continue a calculation with bad data.  
  
Also that amount of performance sacrifice is infinitesimal compared to the duration of the calculation itself, generally speaking.

---

## Comment 5

> Username: pulver  
> Created_at: 2019-01-14 22:52:04 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454192022  

> it seems like the API could be slightly more ergonomic, and some effort on this point would have very high return.  
  
What else did you have in mind beyond the separate non-member `derivative()` function? One primary goal has been to keep the API as minimal/simple and intuitive as possible. I would be very interested to hear recomendations that would support this goal.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2019-01-15 16:42:58 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454461624  

One question: The maximal derivative order is a compile time parameter, so why didn't you use (say):  
  
```cpp  
derivative<3>(y)  
```  
  
since all valid indices are <= order, which for most use cases is <= 2. Then you could do compile-time checking on the index, rather than runtime. (You're right that it isn't a big cost, but people generally don't like throws when they could have a compiler error.)  
  
If the derivative order got very large, the user could iterate using boost.hana.

---

## Comment 7

> Username: pulver  
> Created_at: 2019-01-15 16:53:34 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454465753  

> One question: The maximal derivative order is a compile time parameter, so why didn't you use (say):  
>   
> ```c++  
> derivative<3>(y)  
> ```  
  
Run-time selection of derivative out of a compile-time selection of a maximum derivative is a likely use-case, similar to how run-time selection of std::array<> index is done. We can certainly add the compile-time selection of derivative as you suggest, but I wanted to be more reactive to such features rather than risk proactive addition of such features that may or may not actually be useful.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2019-01-15 17:06:20 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454470601  

Yesterday I tried it out with the Lambert-W function. It didn't compile because of at one point `lambertw_0` has a `return -1`. So we either need a constructor for the autodiff variables on an int, or to make very small diffs (`return T(-1)`) throughout the special function library to make sure they all work with autodiff.  
  
My preference is to have a constructor on an int, because userspace code will also have this problem.

---

## Comment 9

> Username: pulver  
> Created_at: 2019-01-15 17:42:40 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454483514  

> Yesterday I tried it out with the Lambert-W function. It didn't compile because of at one point `lambertw_0` has a `return -1`. So we either need a constructor for the autodiff variables on an int, or to make very small diffs (`return T(-1)`) throughout the special function library to make sure they all work with autodiff.  
>   
> My preference is to have a constructor on an int, because userspace code will also have this problem.  
  
There is a useful [comment](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/round.hpp#L73) from round.hpp that addresses this:  
  
```  
// The following functions will not compile unless T has an  
// implicit convertion to the integer types.  For user-defined  
// number types this will likely not be the case.  In that case  
// these functions should either be specialized for the UDT in  
// question, or else overloads should be placed in the same   
// namespace as the UDT: these will then be found via argument  
// dependent lookup.  See our concept archetypes for examples.  
```  
  
In the case of `iround()`, it was [overloaded](https://github.com/pulver/autodiff/blob/85d34700537a530472e8b22970b658dfd49065af/include/boost/math/differentiation/autodiff.hpp#L1750) into autodiff.hpp.  
  
Independent of autodiff, any function that returns a type T should return values of -1 as  
  
    return static_cast<T>(-1);  
  
which is the [change](https://github.com/boostorg/math/commit/bdbf7d020623805aacacc33728d49f8898c931de) that was recently made to round.hpp for this same reason. Is generally safe to assume/require that custom types T should always **implicitly** take all fundamental arithmetic types in a constructor?  
  
For example, [trunc.cpp](https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/trunc.hpp#L27) does this static casting deliberately:  
  
    return (v >= 0) ? static_cast<result_type>(floor(v)) : static_cast<result_type>(ceil(v));

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2019-01-15 18:02:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454490623  

>Is generally safe to assume/require that custom types T should always implicitly take all fundamental arithmetic types in a constructor?  
  
I don't know about *all* fundamental types, but things like:  
  
* Returning 1 or 0.  
* multiplying/dividing by 2,4,8 etc.  
* adding to unity.  
  
Are going to be ubiquitous as Nick says, and in general are more efficient when written _without_ the explicit cast.  The only circumstance I can think of where conversion from int is unsafe/lossy, is when we have an integer type which has more bits "precision" than the float type - in which case the conversion preserves value, but may lose precision depending on the value.  even so, I would personally allow all conversions from integer types.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2019-01-15 18:06:35 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454491875  

>A link to my more in-depth documentation will be provided which uses MathJax for the notation.  
  
Even better would be to render the Tex-based Math-reference as a PDF and we'll include it with the distribution - that way there's no external website that may get lost over time.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2019-01-15 18:12:03 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454493729  

>My preference would be to keep the svgs in raw form in the repo, rather than generating them, since that'd add another compile step. In addition, I don't feel bad about editing svgs by hand as typos get found, so one of the primary reasons for having a script generate the svgs on the fly, at least for me, is not that important.  
  
That's fair enough, though my long term hope is that we'll be able to squeeze MathML into the html output so having the source around is useful (as all the equations written by me were in MathML as the original format anyway).  
  
BTW had a quick experiment with tex2svg and mml2svg from the mathjax-node-cli package and they work a treat, although the interface is terribly primitive, to process a file you need to use:  
  
```  
tex2svg "$(cat filename.tex)" > filename.svg  
```  
  
Which is Unix-only (or cygwin) sadly.

---

## Comment 13

> Username: pulver  
> Created_at: 2019-01-15 18:54:35 UTC  
> Updated_at: 2019-01-15 18:56:03 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454507926  

> > Is generally safe to assume/require that custom types T should always implicitly take all fundamental arithmetic types in a constructor?  
>   
> I don't know about _all_ fundamental types, but things like:  
>   
>     * Returning 1 or 0.  
>     * multiplying/dividing by 2,4,8 etc.  
>     * adding to unity.  
  
The only one that needs to be "fixed" is the first one. Arithmetic operations are handled by implicit casting to the root_type. For example say `x` is of type `autodiff<double,3>`. Then calling `x*2` invokes the `operator*(double)` which means the `int` 2 is implicitly cast to `double` when calling the operator.  
  
It is only when returning an arithmetic type should the explicit cast be made.  
  
I have tried making the `autodiff(const root_type&)` constructor implicit, but then this causes ambiguity compiler errors when calling all of the overloaded operators. Hence it must be kept explicit. It seem the solution is to either add explicit constructors to `autodiff` for all of the fundamental arithmetic types, or functions that return type T should return `static_cast<T>(1)`. The later seems to me to be more desirable, even independent of autodiff.

---

## Comment 14

> Username: NAThompson  
> Created_at: 2019-01-15 19:18:05 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454515899  

> I have tried making the autodiff(const root_type&) constructor implicit, but then this causes ambiguity compiler errors when calling all of the overloaded operators. Hence it must be kept explicit. It seem the solution is to either add explicit constructors to autodiff for all of the fundamental arithmetic types, or functions that return type T should return static_cast<T>(1). The later seems to me to be more desirable, even independent of autodiff.  
  
It is, presumably, better to `static_cast<T>(1)` than `return 1`. But the only time the latter is harmful is when using `autodiff`. I expect this to severely limit usage, as many devs don't even have the option of touching the code they want to differentiate. Is there a workaround for the compiler problems? Maybe add another explicity constructor for ints?

---

## Comment 15

> Username: pulver  
> Created_at: 2019-01-15 19:26:41 UTC  
> Updated_at: 2019-01-15 19:33:58 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454518855  

> But the only time the latter is harmful is when using autodiff.  
  
It is harmful for any type that doesn't have an explicit or implicit constructor that accepts int. Perhaps autodiff is one of the only known types for which this is true, but I wouldn't necessarily assume it is the only one.  
  
> I expect this to severely limit usage, as many devs don't even have the option of touching the code they want to differentiate.  
  
I am actually assuming they will. Most functions are first written accepting and returning type `double`. They will need to re-write it in template form. Part of doing this will involve properly casting their return types.  
  
> Maybe add another explicity constructor for ints?  
  
This is a slipperly slope toward adding all of the fundamental types, but more importantly, it is extending responsibility to make up for improper return statements which should do proper casting. I would prefer to point developers to improve their code instead.

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2019-01-15 19:40:30 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454523732  

>I beg to differ. The comment in round.hpp I quoted earlier, "The following functions will not compile unless T has an implicit convertion to the integer types. For user-defined number types this will likely not be the case." So it is not just autodiff - it is any type T anticipated by whomever wrote this.  
  
That would be me ;)  
  
But you're misreading that: we assume that conversion from T -> int is not implicit, and maybe not easily possible at all, hence the existence of itrun/iround etc.  The opposite is int -> T which we do assume is implicit and harmless.  
  
I sympathise about the overload ambiguities though - been there got the T-shirt!

---

## Comment 17

> Username: pulver  
> Created_at: 2019-01-15 19:52:00 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454527598  

> But you're misreading that: we assume that conversion from T -> int is not implicit, and maybe not easily possible at all, hence the existence of itrun/iround etc. The opposite is int -> T which we do assume is implicit and harmless.  
  
Indeed I was - quickly edited it after posting. Sorry for the confusion.  
  
So I just tried adding an int constructor:  
  
    explicit dimension(int); // Initialize a constant  
  
The problem with this is how variables are initialized. For example, now this kind of declaration is unintentionally calling the above contructor:  
  
    autodiff<double,3> x(2);  
  
since 2 is intepreted as an int. This means it is not longer a "variable" in the sense that it represents the polynomial 2+epsilon (as explained in the math docs). It is just 2 and no derivatives will be calculated for it.  
  
We could make the new constructor initialize a variable (2+epsilon) but then this would cause confusion because a function which returns just 0 or 1 is presumably not intending for the return value to be a variable with epsilon terms.  
  
I'll think more about this but at present I don't see how to make this work as intended (apart from functions properly casting their return values.)

---

## Comment 18

> Username: pulver  
> Created_at: 2019-01-15 20:32:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454540614  

Fyi I am considering the following signficant refactoring of constructors:  
  
    autodiff<double,3> x{2}; // Initialize a variable (2+epsilon). Curly braces required.  
    autodiff<double,3> x(2); // Initialize a constant (no epsilon terms).  
  
This way statements like `return 2` or even `static_cast<T>(2)` for `T=autodiff<>` will invoke the second way which returns a constant as would be intended in the typical case. The downside is that it is a little abnormal to require curly braces for declaring variables of differentiation. (These two forms are currently reversed in the current implementation.)  
  
Essentially there needs to be a way to distinguish declaring constants from variables. Given the second form above is reserved for returning constants, that leaves either the first way using curly braces for declaring variables, or something like:  
  
    autodiff<double,3> x(2,true);  
  
which I had done in an older version, which I thought was clumsy. I welcome feedback on this as I mull it over.

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2019-01-15 20:38:29 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454542304  

>Fyi I am considering the following signficant refactoring of constructors:  
  
>autodiff<double,3> x{2}; // Initialize a variable (2+epsilon). Curly braces required.  
>autodiff<double,3> x(2); // Initialize a constant (no epsilon terms).  
  
I think I'd rather not have the implicit constructor than have those mean different things - that absolutely will trip people up - the whole point of the "unified initialisation syntax" is that it's well... unified.

---

## Comment 20

> Username: pulver  
> Created_at: 2019-01-15 20:51:42 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454546505  

> the whole point of the "unified initialisation syntax" is that it's well... unified.  
  
Agreed. It seems that the single-parameter constructor then should be reserved for constants, whether they are implicit or explicit. So `return 1` or `return static_cast<T>(1)` both should be returning constants rather than variables. Thus a separate constructor is needed for variables.  
  
I'm considering the following constructor:  
  
    explicit dimension(const root_type& ca, bool is_variable=false);  
  
Then it is invoked in two ways:  
  
    autodiff<double,3> x(2,true); // Initialize a variable (2+epsilon).  
    autodiff<double,3> x(2); // Initialize a constant (no epsilon terms).  
  
`return static_cast<T>(1)` invokes the second way.

---

## Comment 21

> Username: NAThompson  
> Created_at: 2019-01-15 21:40:52 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454562124  

> I'm considering the following constructor:  
```cpp  
explicit dimension(const root_type& ca, bool is_variable=false);  
```  
  
> Then it is invoked in two ways:  
  
```cpp  
autodiff<double,3> x(2,true); // Initialize a variable (2+epsilon).  
autodiff<double,3> x(2); // Initialize a constant (no epsilon terms).  
```  
  
  
>    return static_cast<T>(1) invokes the second way.  
  
This seems like the wrong default.  The Lambert-W function is a case in point. I write `y = lambertw0(x)` with `x` an autodiff variable. If `x==-1/e` then you're proposed syntax would make `y` a constant (since it returns `static_cast<T>(-1)` for that value of `x`), and a variable for all other values of `x`.  
  
Now, I am no expert on automatic differentiation. But it seems like distinguishing between constants and variables cannot be done in full generality at the source code level. Indeed it depends on how the values are used after return.  
  
I would just forward the integer constructor to the `RealType` constructor with a cast, and not treat literal ints and different than (say) doubles. But if you can produce a minimal counterexample which shows how this is the wrong idea I can of course change my mind.

---

## Comment 22

> Username: pulver  
> Created_at: 2019-01-15 22:13:07 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454571999  

> The Lambert-W function is a case in point.  
  
Say this is the setup under the new constructors:  
  
    autodiff<double,3> x(-1/M_E,true);  
  
Though `x` is a "variable", since `==` is overloaded, `x==-1/M_E` still evaluates to true. In general all of the comparison operators only compare the "real" value. That is, the constant term in the polynomial they all represent. So for instance, it may be counter-intuitive but if `x` and `y` are both autodiff variables representing (2+3\*epsilon) and (2+4\*epsilon) respectively, then `x==y` is true since only the constant terms are compared. I believe this is desired in the way that people will code their logic in the evaluation of a function, and I cannot think of any use-case outside of debugging where one would want to actually compare each term of the polynomial. This has never been a problem in practice during the time I have used a past version of this library.  
  
Returning `static_cast<T>(-1)` will indeed return a constant, so `y.derivative(i)==0` for all `i>0`. This is indeed one limitation of AD in general (not just this implementation), and these kinds of things need to be taken into consideration when coding a function. I need/should write documentation on this and related points.  
  
> But it seems like distinguishing between constants and variables cannot be done in full generality at the source code level.  
  
When possible, all values need to be evaluated in terms of an analytic function in order to keep the derivatives. For example, though the gamma function is known at integer points (factorials) it should be evaluated in terms of the continuous gamma function if derivatives are desired at those points. But then again, these kinds of functions should be specifically overloaded in the autodiff library so that higher-order derivatives are accurately calculated.

---

## Comment 23

> Username: pulver  
> Created_at: 2019-01-16 17:40:58 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454871364  

### Current Problems  
1. `autodiff<double,3>(2)` and `autodiff<double,3>{2}` mean different things (initialize variable and constant, respectively). I agree that these need to mean the same thing.  
2. `return static_cast<T>(2)` returns a variable (invokes first constructor above) whereas this should instead return a constant. Otherwise when the return value is assigned to `y`, then `y.derivative(1)==1` which can confuse people. Better to have `y.derivative(i)==0` for all `i>0` so they can improve their function to calculate the return value within a locally analytic function in order to auto-calculate the derivatives.  
3. `return 2` gives a compiler error for functions with generic return type T, when T=`autodiff<double,3>`.  
  
### Proposed Solutions  
1. Delete separate explicit `autodiff(const std::initializer_list<root_type>&)` constructor, and modify explicit `autodiff(const root_type&)` constructor to initialize a constant instead of a variable.  
2. Add new function `boost::math::differentiation::make_variable<RealType,Orders...>(const root_type&)` that returns an `autodiff<RealType,Orders...>` variable. This effectively replaces the old single-parameter constructor for initializing a variable of differentiation.  
3. Add new explicit constructor `autodiff<RealType,Orders...>(int)` to appease template functions that don't cast their return statements. E.g. `return 2` when return type is T.  
  
I think we're in agreement on (1). I welcome feedback on (2). For (3) I would prefer to not do, and instead have the function writer properly cast their return values; however I don't mind (too much) acquiescing to a 2-to-1 vote on this.

---

## Comment 24

> Username: NAThompson  
> Created_at: 2019-01-16 18:36:08 UTC  
> Updated_at: 2019-01-16 20:02:14 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454890413  

> Better to have y.derivative(i)==0 for all i>0 so they can improve their function to calculate the return value within a locally analytic function in order to auto-calculate the derivatives.  
  
I don't think people will in general be able to improve their functions to use locally analytic evaluations. The Lambert-W function is a case in point: At `x==-1/e`, the function is not analytic. The best representation of the derivative at this point would be `std::numeric_limits<Real>::infinity()`, but since there is no information that can be propagated through a calculation that could potentially be used to extract this value, your feature will never be able to return the best derivative.  
  
The documentation should make it clear that at isolated points, the derivatives can be wrong. To take an extreme example, if we were to evaluate `f(x) = 3*x*x*x` via an enormous lookup table, your dual numbers would not be able to propagate the infinitesimals through the computation, and the derivatives would be wrong.  But also, it would be nice to have a review article which discusses how other authors have addressed this problem and mitigated it, if at all. I found [this book](https://www.amazon.com/Evaluating-Derivatives-Principles-Algorithmic-Differentiation/dp/0898716594) which hopefully has some insight into the problem. Chapter 14 and Table 2.3 are especially relevant to this discussion.  
  
As to point 2, I would just like to know that writing:  
  
```cpp  
double x = 2;  
autodiff<double, 2> ax(x);  
```  
  
and  
  
```cpp  
autodiff<double, 2> ax(2);  
```  
  
have exactly the same meaning.

---

## Comment 25

> Username: NAThompson  
> Created_at: 2019-01-16 19:06:33 UTC  
> Updated_at: 2019-01-16 20:31:52 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454900690  

It would be very interesting to me to see how [Stan](https://github.com/stan-dev/math/) deals with these problems. They've been autodiff'ing for years, so we could learn a lot from their API and documentation. Another library which seems quite nice is [Adept](http://www.met.reading.ac.uk/~swrhgnrj/publications/adept.pdf) which supports forward mode AD.

---

## Comment 26

> Username: pulver  
> Created_at: 2019-01-16 20:18:19 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-454924722  

> I don't think people will in general be able to improve their functions to use locally analytic evaluations.  
  
Unfortunately that's a requirement of AD - not just this implementation. As you pointed out, if one were to write a function as just a lookup table, then no derivatives are computed.  
  
The solution, which both autodiff and other libraries have done, is to implement their own versions of such mathematical functions. For example: [atan()](https://github.com/pulver/autodiff/blob/85d34700537a530472e8b22970b658dfd49065af/include/boost/math/differentiation/autodiff.hpp#L1717). The first derivative is 1/(1+x*x), and by using autodiff itself on this expression, it can calculate an arbitrary number of higher order derivatives. So this fact is incorporated so that higher order derivatives of atan() are calculated reliably. Similar mathematical insights/techniques are used to overload other functions. This is what *should* be done with Lambert-W. Otherwise we are just calculating derivatives of the approximation, not the mathematical function itself.  
  
There are 4 public helper functions [apply(), apply_with_factorials(), etc.](https://github.com/pulver/autodiff/blob/85d34700537a530472e8b22970b658dfd49065af/include/boost/math/differentiation/autodiff.hpp#L762) to assist in writing such custom functions that essentially amount to passing in a lambda(n) which returns the nth derivative using straight numerical values (e.g. double) without having to deal with epsilons, polynomials, etc. Yes I need to document this too.  
  
> As to point 2, I would just like to know that writing: `<snip>` have exactly the same meaning.  
  
Yes they do now and will with the proposed changes. However the meaning of both will change from representing variables (2+epsilon) to a constant (2). The new way to declare a variable of differentiation will be:  
  
    autodiff<double,2> ax = make_variable<double,2>(2)  
  
or more compactly:  
  
    auto ax = make_variable<double,2>(2)

---

## Comment 27

> Username: NAThompson  
> Created_at: 2019-01-17 01:00:56 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455002521  

Quick question: Should the file name be changed to (say) `forward_mode_autodiff.hpp` to give us the ability to add `reverse_mode_autodiff.hpp` in the future? It looks like Hamiltonian Monte-Carlo and a fast Newton's method for f:R^n->R will require a reverse-mode autodiff.

---

## Comment 28

> Username: pulver  
> Created_at: 2019-01-17 17:25:22 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455256978  

> Should the file name be changed  
  
Ok. How about `autodiff_forward.hpp` and `autodiff_reverse.hpp` so that they are grouped together alphabetically? This also raises the question of naming classes/functions/aliases. Perhaps:  
  
    using boost::math;  
    differentiation::fvar<double,3> x = differentiation::make_fvar<double,3>(2); // for forward  
  
Note however that this **doesn't imply** that reverse mode will take the same form:  
  
    differentiation::rvar<double,3> x = differentiation::make_rvar<double,3>(2); // for reverse  
  
In fact I think more likely it will NOT be the case that it will look like this. The reason is that it seems to me that reverse mode will require use of dynamic memory. Therefore it need not make the constraining optimization that forward mode makes in setting the maximum derivative orders at compile time. In fact it's probably a good idea to intentionally leave this possibility open, in case an application wants to set the max derivative order during run-time, then the reverse mode code of the future will be the natural choice, even if it isn't faster than forward mode for their particular application.

---

## Comment 29

> Username: NAThompson  
> Created_at: 2019-01-17 17:50:39 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455265598  

I like the filenames you’ve came up with. As to the variable names, I wouldn’t worry about changing them unless you can see a clear conflict with a reverse mode autodiff in the future. Personally I don’t know enough about reverse mode autodifferentiation to see the places for potential conflict and it seems like it will put a very large burden on you to future proof while writing your first boost feature. We’ll wait for John’s opinion on this though; if there are clearly visible forward compatibility problems we can see we should solve them.

---

## Comment 30

> Username: pabristow  
> Created_at: 2019-01-17 18:17:07 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455274101  

I've been lurking following this thread.  Sounds generally good to me.  Can I just say that since forward autodiff is by the most common, can you have just plain autodiff for the common case and autodiff_reverse for the unusual case.  This will show nicer in the index, putting the two next to each other.  I think that nobody is going to search for reverse first.

---

## Comment 31

> Username: NAThompson  
> Created_at: 2019-01-17 22:13:31 UTC  
> Updated_at: 2019-01-17 22:16:31 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455351629  

> Can I just say that since forward autodiff is by the most common, can you have just plain autodiff for the common case and autodiff_reverse for the unusual case.  
  
I would actually contend that reverse-mode is the most common case. You want a forward mode autodiff for f:R^{n}\to R^{m} where m >> n, and reverse mode for n >> m. In particular, you want reverse mode for quantum mech, since that's a map from R^{3N}->C. For celestial mechanics, you have a map R^{6N}->R^{6}.

---

## Comment 32

> Username: pabristow  
> Created_at: 2019-01-18 15:56:06 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455594340  

I stand in a state of ignorance and corrected, but as far as users seeking autodiff, I still think that the first thing theywill be seeking is autodiff, so that should be first.  Seeing the next item autodiff_reverse will make is obvious that there is a direction to consider. Your bikesheddingly...

---

## Comment 33

> Username: pulver  
> Created_at: 2019-01-18 16:40:39 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455609738  

I'm increasingly interested in and accepting of the idea of eventually adding reverse mode AD to boost. So here are some proposed naming conventions that incorporate what has been said so far, keeping in mind that reverse mode may be added in the future without having to do too much future-proofing:  
  
    #include <boost/math/differentiation/autodiff.hpp>  
  
    using boost::math;  
    differentiation::autodiff_fvar<double,2,3> x = differentiation::make_fvar<double,2,3>(5);  
  
where `autodiff_fvar` is a template alias that constructs nested classes from `differentiation::detail::fvar<RealType,Order>` (currently called `dimension<RealType,Order>`.)  
  
In the future, if/when reverse mode is added, the contents of `autodiff.hpp` can be moved to `autodiff_forward.hpp` and `autodiff.hpp` can `#include` both `autodiff_forward.hpp` and `autodiff_reverse.hpp`, and no one will need to change their code for this upgrade.  
  
Likewise, the naming convention for the reverse mode aliases/classes/functions will use rvar in place of fvar above. We don't need to decide what form the template/function parameters will be yet, but some degree of anticipation of these names seems appropriate and sensible.

---

## Comment 34

> Username: NAThompson  
> Created_at: 2019-01-18 16:57:37 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455615273  

> In the future, if/when reverse mode is added, the contents of autodiff.hpp can be moved to autodiff_forward.hpp and autodiff.hpp can #include both autodiff_forward.hpp and autodiff_reverse.hpp, and no one will need to change their code for this upgrade.  
  
Yup, this looks like the correct decision to me.

---

## Comment 35

> Username: pabristow  
> Created_at: 2019-01-18 16:59:18 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-455615870  

I agree.  Covers all cases (unless there is an up or down ;-)

---

## Comment 36

> Username: bgoodri  
> Created_at: 2019-01-26 06:18:18 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-457806367  

I'm one of the Stan developers, although I haven't dealt with the core autodiff code all that much.   
  
Fast reverse mode autodiff is the primary use-case for Stan, but I am sure we would be interested in this PR because Stan's implementation of forward mode autodiff has been incomplete for more than five years and we have been thinking about abandoning it.  
  
Regarding declarations, the Stan language (which gets transpiled to C++) has a block structure that defines whether something is a constant --- in which case we do not want to differentiate with respect to it and it becomes either `int` or `double` in the C++ --- or a variable that becomes a custom type in the C++. In the somewhat rare cases where someone is writing C++ by hand that utilizes the Stan library, then  
```  
stan::math::var x = 2; // variable  
double y = 3; // constant  
auto z = x + y; // variable with the second term cast to a variable  
```  
So, we don't run into a situation where something would be intended as a constant but not declared as a `double` or an `int`. And then all of Stan's functions are templated. I don't know if that helps.  
  
Another Stan developer, @betanalpha , has recently written a theoretical overview of autodifferentiation from the perspective of geometry  
https://arxiv.org/abs/1812.11592  
that might be useful.

---

## Comment 37

> Username: pulver  
> Created_at: 2019-01-26 16:38:18 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-457845282  

> we don't run into a situation where something would be intended as a constant but not declared as a `double` or an `int`.  
  
Similar reasoning also motivated the design of the current (now older) version. Based on the feedback from this PR, and also considering how easy it will be for new users to accidentally create a variable which should be a constant (for example, returning 1 from a template function whose return type is `T=var` would return a variable&mdash;but it should be a constant) suggests it should be made more deliberate when instantiating a variable. I am now satisfied with a template function, e.g. `make_fvar<double,3>(2)` in order to create variables. To mirror your example, the new API looks like:  
  
```c++  
autodiff_fvar<double,3> x = 2; // constant  
double y = 3; // constant  
auto z = x + y; // constant of type autodiff_fvar<double,3>  
  
autodiff_fvar<double,3> u = make_fvar<double,3>(2); // variable  
z = u + y; // variable  
```  
  
This is not too unusual; it's somewhat similar to `std::make_unique<T>(5)` for example. `make_fvar` is simply calling a new 2-parameter constructor:  
  
```c++  
fvar(const root_type&, bool is_variable);  
```  
which can also be called directly, but is not very aesthetically pleasing:  
```c++  
autodiff_fvar<double,3> x(2,true); // variable  
```  
---  
Regarding a previous but related discussion about adding an implicit `fvar(int)` constructor to allow for uncast returns (`return 1`) from a template function with return type `T`&mdash;one problem with this is now if someone does `return 0.5` then the `0.5` will be implicitly cast to `0` and quietly introduce a significant error into the calculation (in particular, if root_type=cpp_bin_float_50 then the `explicit fvar(const root_type&)` constructor will not be called in this case.) Fortunately there seems to be a clean solution to this, which is to simply use a template constructor:  
  
```c++  
template<typename RealType2>  
fvar(const RealType2& ca);  
```  
  
Basically any type `RealType2` that can be cast to `root_type` is a valid implicit cast. If not, then `static_cast<root_type>(ca)` will cause a compile-time error. This also doesn't cause ambiguity errors with all of the overloaded operators, which would happen were it not a template function. In addition this addresses my previous concern about `fvar(int)` being a slippery slope toward adding implicit constructors for all of the arithmetic types&mdash;just slide all the way down the slope using a generic type :)

---

## Comment 38

> Username: jzmaddock  
> Created_at: 2019-01-30 21:29:40 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-459116668  

Hopefully I've now pushed a fix for the cpp_bin_float ambiguity issue to multiprecision develop.

---

## Comment 39

> Username: pulver  
> Created_at: 2019-01-30 22:52:17 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-459142641  

> Hopefully I've now pushed a fix for the cpp_bin_float ambiguity issue to multiprecision develop.  
  
Works great! Confirmed by `std::is_convertible` as well. Cheers.

---

## Comment 40

> Username: pulver  
> Created_at: 2019-02-04 17:41:40 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-460341443  

As far as I can tell, this PR is ready to be pulled into the `develop` branch. (Not sure why the appveyor CI tests are pending, however.) I believe I've incorporated all of the important suggestions/concerns in an agreeable manner, but please correct me if I have missed anything.  
  
For completeness, I'll address and resolve all of the comments, unless you think that results in more noise (aka spam) than signal.

---

## Comment 41

> Username: NAThompson  
> Created_at: 2019-02-06 02:57:03 UTC  
> Updated_at: 2019-02-07 17:26:07 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-460884804  

Cool, it looks much better. A couple comments:  
  
- We won't be able to edit `doc/differentiation/autodiff.pdf`, so if you get hit by a bus, then this file is going to quickly get out of date.  
- I would've liked to see a `[heading Caveats]` in the `.qbk`, which explains the ways that automatic differentiation can fail, as well as the ways your particular implementation can fail. Presumably this would reference the discussion in "Evaluating Derivatives", by Andreas Griewank. Griewank has a nice list of rules for when AD yields useful results; since Griewank's book is not widely read it may be good to quote a few. He uses the example of sinc(x) = sin(x)/x evaluated via if(x==0) { return 1;} else return sin(x)/x, whose second derivative is always wrong via automatic differentiation. Griewank chapter 14 is the most simple and cogent discussion about the limitations of AD that I have read.  
- What does the `f` stand for in `make_fvar`?  
- The digits of the example go off my screen. Maybe use `cpp_bin_float_50` instead of `cpp_bin_float_100`? I think it's clear that the result is very accurate.  
- I will make sure all the "non-differentiable" functions in Griewank, table 2.3 can be used by this library in a couple days; but in the meantime what should we do about the Heaviside function?  
- The test cases are now very extensive, and I am convinced that this works as it is supposed to.  
- That said, I would still like to see that every special function in the boost.math library can be compiled with autodiff. I'm not too concerned about demonstrating correctness (that, in my mind, is complete), maybe only a compile test needs to be added? `#include<boost/math/special_functions.hpp`, and just instantiate as many of these as you can until it breaks, and document which ones are impossible to use with autodiff (`float_distance` and `next` come to mind). That will demonstrate that this library is consistent with the rest of the library. Here's the beginning of a compile test:  
  
```cpp  
#ifdef _MSC_VER  
#pragma warning(disable:4459)  
#endif  
  
#include <boost/math/special_functions.hpp>  
#include <boost/math/differentiation/autodiff.hpp>  
#include "test_compile_result.hpp"  
  
void compile_and_link_test()  
{  
    auto x = boost::math::differentiation::make_fvar<double,5>(2.0);  
    auto y = boost::math::airy_ai(x);  
    check_result<double>(y.derivative(0));  
  
  
    y = boost::math::lambert_w0(x);  
    check_result<double>(y.derivative(0));  
}  
```  
  
I've tentatively put this in `test/compile_test/autodiff_sf_compatibility_test.cpp`. As you will see, `airy_ai` doesn't compile, but either some changes will need to be made in the `special_functions` directory, or you'll need to add some constructors.  
  
- I still think there are wins to be had in choosing variable names more carefully, but since I have no productive suggestions, I will shut up about it. (Though I still like the syntax `derivative<3>(y)` above `y.derivative(3)`. This is of course not a showstopper.)  
- I ran everything under ASan and UBSan, and it's still good. cppcheck is clean.  
- I still feel antipathy towards `autodiff_cpp11.hpp`. I feel like this file will not age gracefully.  
- Could you write a section on "Migrating code to be compatible with automatic differentiation". This would take a function  
  
```cpp  
double foo(double x) {  
   return std::sin(x)*std::tan(std::sqrt(x*x+1)); // whatever  
}  
```  
  
and show how to migrate this to be automatically differentiated.  
  
I will be going over this more-basically looking for the edge cases discussed in Griewank, then I'll try to write a vectorized Newton's method on top of it, and then I think I'll be comfortable signing off on it. John will presumably also have more requirements, but this is getting ready for primetime.

---

## Comment 42

> Username: bgoodri  
> Created_at: 2019-02-06 04:49:56 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-460900335  

If you want to add some strenuous autodiff tests once the hypergeometric functions PR has been merged, complicated derivatives of Bessel functions with respect to the order have been worked out at  
https://blog.wolfram.com/2016/05/16/new-derivatives-of-the-bessel-functions-have-been-discovered-with-the-help-of-the-wolfram-language/  
See the end of that blogpost for links to download the results.

---

## Comment 43

> Username: pulver  
> Created_at: 2019-02-08 16:30:31 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-461861397  

Thanks again for the useful feedback.  
  
Some of the points are still in progress, but I'll respond to some of them now.  
  
fvar stands for forward-mode autodiff variable. I'll add this to the doc. This is in anticipation of rvar in the future, which I imagine will have a similar interface: autodiff_rvar and make_rvar.  
  
Regarding heaviside, I'd say it be omitted for now until someone encounters a real-world need for it. It's not clear to me how higher order derivatives should be handled at x=0. I have some ideas for how it might be implemented, but without a real-world application I fear developing something will be off-the-mark, and when someone does come with a real-world application, then the fix will break someone else's old code. I'd rather be experimentally reactive in this respect, rather than theoretically proactive.  
  
Regarding the special functions, @kedarbhat has kindly agreed to take this on. Out of the ones I did get through, the most common incompatibility were calling math functions that require a Policy parameter, thereby preventing ADL calls of the autodiff math functions. For example, `iround(v,pol)` called by `airy_ai()` rather than just `iround(v)`. Second to this were calls to functions that are not officially on the [Conceptual Requirements for Real Number Types](https://www.boost.org/doc/libs/1_69_0/libs/math/doc/html/math_toolkit/real_concepts.html), which I have been adding to autodiff as a result (e.g. `sinh()`, `acosh()`, etc.) I suppose I should make a separate PR for updating the real_concepts.html page.  
  
Side note: a `lambert_w0()` overload was added for [calculating](https://github.com/pulver/autodiff/blob/9e510f4e8633243c88f661c3659bcdf41906c497/test/test_autodiff.cpp#L1662) arbitrarily high orders of derivatives.

---

## Comment 44

> Username: pulver  
> Created_at: 2019-02-12 13:11:25 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-462753157  

> I would've liked to see a [heading Caveats] in the .qbk, which explains the ways that automatic differentiation can fail, as well as the ways your particular implementation can fail.  
  
This has been added to the documentation:  
  
> At a high level there is one fairly simple principle, loosely and intuitively speaking, to writing functions for which autodiff can effectively calculate derivatives:  
>   
> **Autodiff Function Principle (AFP)**  
>   
> > A function whose branches in logic correspond to piecewise analytic calculations over non-singleton intervals, with smooth transitions between the intervals, and is free of indeterminate forms in the calculated value and higher order derivatives, will work fine with autodiff.  
>   
> Stating this with greater mathematical rigor can be done.  However what seems to be more practical, in this case, is to give examples and categories of examples of what works, what doesn’t, and how to remedy some of the common problems that may be encountered.  That is the approach taken here.  
  
Examples of errors, ways to fix them, and general principles follow. I've kept this in the pdf (latex file has been added) since it is fairly long, involves a fair amount of latex, and there doesn't seem to be an easy way to keep it in sync with qbk. In any case I think it is somewhat of an advanced concept, or at least it is hard to separate out any significant part of it from more in-depth concepts and discussion. There is enough in the .qbk for someone to get started with, and refer to the pdf for more info.

---

## Comment 45

> Username: pabristow  
> Created_at: 2019-02-12 16:41:29 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-462835063  

I am very sympathetic to you not wanting to try to redo the fearsome equations in Quickbook - or indeed in anything else!  
  
It would be good to be able to maintain this by editing the .tex file, so we should have this in the /doc folder.  (It would be really, really good if anyone could figure out how to get latex into quickbook).  
  
It would be most valuable to users to be able to have these examples given in full working order in the /examples folder.  They can be 'tested' by including in the /examples/jamfile.v2.  
Users can just build any or all of the examples by dropping the cpp into their preferred build system.  
  
I think you have .cpp file(s) that do this already, so it is only a matter of adding them to /examples.  
(And making sure of copyright and licences of course).  
  
Even better would be to use the Quickbook snippets system - see  
  
https://www.boost.org/doc/libs/1_69_0/doc/html/quickbook/syntax/block.html#quickbook.syntax.block.code  
  
https://www.boost.org/doc/libs/1_69_0/doc/html/quickbook/syntax/block.html#quickbook.ref.import  
  
This will ensure that what is in the docs is exactly what has compiled and run.  
  
(This comment applies to the quadrature section too.  If you want me to perform this tiresome task, I would be willing to do it for you).

---

## Comment 46

> Username: NAThompson  
> Created_at: 2019-02-12 18:45:35 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-462883694  

I have read through your new pdf, and all my concerns about content have been addressed. I found the discussion very cogent, and I think it will allow anyone who wants to use this library succeed. I will defer to @jzmaddock's judgement about whether adding a .pdf to the documentation is appropriate.

---

## Comment 47

> Username: pulver  
> Created_at: 2019-02-16 18:15:01 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-464368820  

### How to fix 'too many sections' error with 2 appveyor mingw tests?  
  
On the latest [appveyor tests](https://ci.appveyor.com/project/jzmaddock/math/builds/22373838), the last 2 failing tests for mingw are due to:  
  
```  
C:/mingw-w64/x86_64-6.3.0-posix-seh-rt_v5-rev1/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/6.3.0/../../../../x86_64-w64-mingw32/bin/as.exe: ..\..\..\bin.v2\libs\math\test\test_autodiff.test\d43c6768c960202759d81fa7e5e89dae\test_autodiff.o: too many sections (54190)  
C:\Users\appveyor\AppData\Local\Temp\1\cc5gOIjM.s: Assembler messages:  
C:\Users\appveyor\AppData\Local\Temp\1\cc5gOIjM.s: Fatal error: can't write 187 bytes to section .text of ..\..\..\bin.v2\libs\math\test\test_autodiff.test\d43c6768c960202759d81fa7e5e89dae\test_autodiff.o because: 'File too big'  
C:/mingw-w64/x86_64-6.3.0-posix-seh-rt_v5-rev1/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/6.3.0/../../../../x86_64-w64-mingw32/bin/as.exe: ..\..\..\bin.v2\libs\math\test\test_autodiff.test\d43c6768c960202759d81fa7e5e89dae\test_autodiff.o: too many sections (54190)  
C:\Users\appveyor\AppData\Local\Temp\1\cc5gOIjM.s: Fatal error: can't close ..\..\..\bin.v2\libs\math\test\test_autodiff.test\d43c6768c960202759d81fa7e5e89dae\test_autodiff.o: File too big  
    "g++"   -fvisibility-inlines-hidden -m64 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -std=gnu++1z -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_STATIC_LINK=1 -DBOOST_UBLAS_UNSUPPORTED_COMPILER=0 -DCI_SUPPRESS_KNOWN_ISSUES  -I"." -I"..\..\.." -I"..\include_private" -c -o "..\..\..\bin.v2\libs\math\test\test_autodiff.test\d43c6768c960202759d81fa7e5e89dae\test_autodiff.o" "test_autodiff.cpp"  
```  
  
This appears to be similar to the MSVC error of too many sections, which is fixed by included a `/bigobj` compiler flag. One suggested [resolution](https://stackoverflow.com/questions/16596876/object-file-has-too-many-sections) for mingw is to include a compiler option `-Wa,-mbig-obj` which I've attempted in `test/Jamfile.v2`:  
  
```  
   [ run test_autodiff.cpp ../../test/build//boost_unit_test_framework : : : <toolset>gcc-mingw:<cxxflags>-Wa,-mbig-obj <toolset>msvc:<cxxflags>/bigobj [ requires cxx11_inline_namespaces ] ]  
```  
however this didn't seem to help. Any suggestions?

---

## Comment 48

> Username: NAThompson  
> Created_at: 2019-02-16 18:54:43 UTC  
> Updated_at: 2019-02-16 19:04:59 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-464371916  

Great question; which I don't have a good answer for. However, I note that most of the tests use the header-only version of Boost.Test, which I think is responsible for a lot of the object file bloat. Raffi (the maintainer of Boost.Test) claims he's got a much better dynamic linking story targeted for release in 1.70, so I was planning on migrating many of the boost.math tests to dynamic linking in a few months.  
  
I should add: I have no clue that this will actually solve the problem, though I have verified that it does significantly reduce compile times locally.  
  
I also think AppVeyor is seeing if they can get away with giving each build less disk space resources, which is probably a safe assumption for 99% of projects!

---

## Comment 49

> Username: jzmaddock  
> Created_at: 2019-02-16 19:39:45 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-464376734  

>How to fix 'too many sections' error with 2 appveyor mingw tests?  
  
This sucks, I know of no solution other than splitting the test into multiple smaller tests :(

---

## Comment 50

> Username: pulver  
> Created_at: 2019-02-21 13:39:10 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466002583  

### test_numerical_differentiation : File too big  
  
It seems that starting with build [1.0.455-develop](https://ci.appveyor.com/project/jzmaddock/math/builds/22317518) the test_numerical_differentiation is failing for the two gcc-mingw TEST_SUITE=misc builds:  
  
> C:\Users\appveyor\AppData\Local\Temp\1\cczd7TIc.s: Fatal error: can't write 187 bytes to section .text of ..\..\..\bin.v2\libs\math\test\test_numerical_differentiation.test\d43c6768c960202759d81fa7e5e89dae\test_numerical_differentiation.o because: 'File too big'  
> C:/mingw-w64/x86_64-6.3.0-posix-seh-rt_v5-rev1/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/6.3.0/../../../../x86_64-w64-mingw32/bin/as.exe: ..\..\..\bin.v2\libs\math\test\test_numerical_differentiation.test\d43c6768c960202759d81fa7e5e89dae\test_numerical_differentiation.o: too many sections (32961)  
  
I'm close to resolving this same issue with autodiff, but once that is fixed this will then become the blocker.

---

## Comment 51

> Username: NAThompson  
> Created_at: 2019-02-21 17:18:39 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466086340  

I just kicked off a build which hopefully will resolve this.

---

## Comment 52

> Username: pulver  
> Created_at: 2019-02-24 14:57:07 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466784090  

> I just kicked off a build which hopefully will resolve this.  
  
Thanks. All autodiff-related tests are passing. The one failing test in appveyor is unrelated:  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
test_root_iterations.cpp(295): error: in "test_main": check iters <= 27 has failed [39 > 27]  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
```

---

## Comment 53

> Username: jzmaddock  
> Created_at: 2019-02-24 18:04:33 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466800420  

>The one failing test in appveyor is unrelated  
  
Yup, that was me - just pushed a fix now.

---

## Comment 54

> Username: pulver  
> Created_at: 2019-02-24 19:00:07 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466805461  

Should I rebuild? Anything else I need to do for this PR?

---

## Comment 55

> Username: NAThompson  
> Created_at: 2019-02-24 22:57:55 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466826890  

Quick question:  
  
If I have a function like a squared L2 norm:  
  
```cpp  
template<class RandomAccessContainer>  
auto l2_sq(RandomAccessContainer const & v) {  
    using Real = typename RandomAccessContainer::value_type;  
    Real t =0;   
    for(auto x : v) {  
        t += x*x;  
   }  
    return t;  
}  
```  
  
how do I autodiff it or refactor it for autodiff'ing?

---

## Comment 56

> Username: pulver  
> Created_at: 2019-02-24 23:44:57 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466830785  

> If I have a function like a squared L2 norm:  
> [snip]  
> how do I autodiff it or refactor it for autodiff'ing?  
  
Here is code for finding the L2 norm-squared for a 3D (x,y,z) vector, with all of its non-zero derivatives, assuming x, y, and z are 3 independent variables.  
  
```c++  
#include <boost/math/differentiation/autodiff.hpp>  
#include <iostream>  
  
template<class RandomAccessContainer>  
auto l2_sq(RandomAccessContainer const & v) {  
    const typename RandomAccessContainer::value_type zero = 0;  
    return std::inner_product(v.cbegin(), v.cend(), v.cbegin(), zero);  
}  
  
int main()  
{  
    using namespace boost::math::differentiation;  
    constexpr int Nx = 5;  
    constexpr int Ny = 5;  
    constexpr int Nz = 5;  
  
    using ad_type = autodiff_fvar<double,Nx,Ny,Nz>;  
  
    std::vector<ad_type> vec {{  
        make_fvar<double,Nx>(10),  
        make_fvar<double,Nx,Ny>(11),  
        make_fvar<double,Nx,Ny,Nz>(12),  
    }};  
  
    ad_type len2 = l2_sq(vec);  
  
    // Print non-zero derivatives  
    for (int ix=0 ; ix<=Nx ; ++ix)  
        for (int iy=0 ; iy<=Ny ; ++iy)  
            for (int iz=0 ; iz<=Nz ; ++iz)  
                if (len2.derivative(ix,iy,iz) != 0.0)  
                    std::cout << "len2.derivative("<<ix<<','<<iy<<','<<iz<<") = "  
                        << len2.derivative(ix,iy,iz) << std::endl;  
    return 0;  
}  
/*  
Output:  
len2.derivative(0,0,0) = 365  
len2.derivative(0,0,1) = 24  
len2.derivative(0,0,2) = 2  
len2.derivative(0,1,0) = 22  
len2.derivative(0,2,0) = 2  
len2.derivative(1,0,0) = 20  
len2.derivative(2,0,0) = 2  
*/  
```

---

## Comment 57

> Username: NAThompson  
> Created_at: 2019-02-25 00:27:37 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466834686  

Ok, but how do I get a gradient for an arbitrary length vector?

---

## Comment 58

> Username: pulver  
> Created_at: 2019-02-25 01:06:05 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466838747  

> Ok, but how do I get a gradient for an arbitrary length vector?  
  
Can you give an example of a specific question with actual numbers, and what the answer should be?

---

## Comment 59

> Username: NAThompson  
> Created_at: 2019-02-25 01:58:14 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466845456  

For $x \in \mathbb{R}^n$, I should get $\nabla ||x||_2^2 = 2x$.  
  
The next one I wanted to try was the Speelpenning example given on Griekwank, page 3:  
  
f(x) := x[0]*x[1]*\cdots * x[n-1]  
  
whose gradient is obvious. But am I correct that this API requires I know how many dimensions I require at compile time?

---

## Comment 60

> Username: pulver  
> Created_at: 2019-02-25 03:10:09 UTC  
> Updated_at: 2019-02-25 03:44:09 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466857108  

> For $x \in \mathbb{R}^n$, I should get $\nabla ||x||_2^2 = 2x$.  
  
That was (hidden) in my previous answer:  
  
```  
len2.derivative(1,0,0) = 20  
len2.derivative(0,1,0) = 22  
len2.derivative(0,0,1) = 24  
```  
where (x,y,z)=(10,11,12). It wouldn't be too hard to wrap this in an API. A method in a future version can also accept a `std::array<>` as a single parameter to `derivatives()` to make this call a bit easier.  
  
If you want the result to actually contain a free variable like `2x`, then my response would be that that is what a *symbolic differentiation* library does, such as Mathematica. Automatic differentiation (forward or reverse) always calculates specific derivatives at specific numeric values.  
  
>   
> The next one I wanted to try was the Speelpenning example given on Griekwank, page 3:  
>   
> f(x) := x[0]_x[1]_\cdots * x[n-1]  
>   
> whose gradient is obvious. But am I correct that this API requires I know how many dimensions I require at compile time?  
  
Yes, the upper-bound number `N` of independent variables is a compile-time requirement. The actual number of variables `n <= N` can still be determined at run-time.  
  
The benefit of this forward-mode implementation is speed, which largely comes from the compile-time calculations it is able to do and avoid any run-time memory allocations.  
  
Since reverse-mode appears to inherently require dynamic memory (in order to store its calculation steps) then calculations which require run-time determination of number of variables is better suited for using a reverse mode library (which may come in the future).

---

## Comment 61

> Username: NAThompson  
> Created_at: 2019-02-25 03:52:24 UTC  
> Updated_at: 2019-02-25 03:53:10 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466862873  

Ok, one more question: In your example you have  
  
std::vector<ad_type> vec {{  
        make_fvar<double,Nx>(10),  
        make_fvar<double,Nx,Ny>(11),  
        make_fvar<double,Nx,Ny,Nz>(12),  
    }};  
  
so the first argument is initialized using different syntax than the second, and the second initialized differently than the third. Is it possible that this can be simplified or does the syntax generate wins?

---

## Comment 62

> Username: pulver  
> Created_at: 2019-02-25 04:21:25 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466867118  

> Ok, one more question: In your example you have  
>   
> std::vector<ad_type> vec {{  
> make_fvar<double,Nx>(10),  
> make_fvar<double,Nx,Ny>(11),  
> make_fvar<double,Nx,Ny,Nz>(12),  
> }};  
>   
> so the first argument is initialized using different syntax than the second, and the second initialized differently than the third. Is it possible that this can be simplified or does the syntax generate wins?  
  
Yes I think it can in principle be simplified. It's probably possible to make the following replacements:  
  
```  
using ad_type = autodiff_fvar<double,Nx,Ny,Nz>;  
// replace with:  
using ad_type = autodiff_fvector<double,3,5>;  
  
std::vector<ad_type> vec {{  
    make_fvar<double,Nx>(10),  
    make_fvar<double,Nx,Ny>(11),  
    make_fvar<double,Nx,Ny,Nz>(12),  
}};  
// replace with:  
auto vec = make_fvector<double,3,5>(10,11,12);  
```  
Still thinking about whether `make_fvector` should return a `vector` or `tuple` (more memory would be saved with a tuple since it wouldn't cast them to be all the same type). But I really hope this isn't required for this version. :) This would probably be best introduced along with vector functions as well.  
  
In terms of the current version, the most it could be simplified would be:  
  
```  
std::vector<ad_type> vec {{  
    make_fvar<double,Nx>(10),  
    make_fvar<double,0,Ny>(11),  
    make_fvar<double,0,0,Nz>(12),  
}};  
```  
  
The syntax is a "win", IMO, in that it forces the user to think in terms of parameter position corresponding to the independent variable. Explicitly:  
  
1. Independent variable x : position 1 in `make_fvar` (`Nx`) : position 1 of `derivative(1,0,0)`.  
2. Independent variable y : position 2 in `make_fvar` (`Ny`) : position 2 of `derivative(0,1,0)`.  
3. Independent variable z : position 3 in `make_fvar` (`Nz`) : position 3 of `derivative(0,0,1)`.  
  
Lastly, this API choice wasn't made in isolation. I have looked at other implementations that use indexes as variables. I can see how this might be more desirable when dealing with a variable number of variables, but for simpler cases, which I have encountered in practice, this present syntax is more intuitive. The more generalized case can easily be accommodated by adding additional methods. I think it is sensible to wait for feedback and requests for this to be sure it satisfies real-world applications.

---

## Comment 63

> Username: NAThompson  
> Created_at: 2019-02-25 05:48:05 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466880131  

Ok so no vector API for now; basically there is a restriction to a number of dimensions that is sane to pass on the stack. I think this is fine, since as you say the R^n->R is more efficient in reverse mode.  
  
Is there a way to do f:R->R^n?

---

## Comment 64

> Username: pabristow  
> Created_at: 2019-02-25 10:02:34 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-466950548  

I hope that this neat example and discussion can be added to the autodiff examples.  Worked examples are really, really useful for users.

---

## Comment 65

> Username: pulver  
> Created_at: 2019-02-25 15:22:51 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-467052154  

> Is there a way to do f:R->R^n?  
  
Example of traversing the unit circle as a function of theta:  
```c++  
  
#include <boost/math/differentiation/autodiff.hpp>  
#include <iostream>  
#include <vector>  
  
template<class Real>  
std::vector<Real> unit_circle(Real const & theta)  
{  
    using namespace std;  
    return { cos(theta), sin(theta) };  
}  
  
int main()  
{  
    using namespace boost::math::differentiation;  
  
    constexpr int N = 4;  
    std::vector<autodiff_fvar<double,N>> p = unit_circle(make_fvar<double,N>(M_PI/3));  
  
    for (int i=0 ; i<=N ; ++i)  
    {  
        std::cout << "Derivative("<<i<<"): ";  
        for (int j=0 ; j<p.size() ; ++j)  
            std::cout << (j?',':'(') << p[j].derivative(i);  
        std::cout << ")\n";  
    }  
    return 0;  
}  
/*  
Output:  
Derivative(0): (0.5,0.866025)  
Derivative(1): (-0.866025,0.5)  
Derivative(2): (-0.5,-0.866025)  
Derivative(3): (0.866025,-0.5)  
Derivative(4): (0.5,0.866025)  
*/  
```  
  
In the case of f:R->R^n, n is truly variable, in that the dimensions of the resulting vector can be determined at run-time. This is because the number of independent variables is known at compile-time (1). The number of dependent variables doesn't have this restriction.

---

## Comment 66

> Username: pulver  
> Created_at: 2019-02-28 13:57:25 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-468281544  

Fyi I just removed the following line that somehow made it into test/Jamfile.v2:  
  
    [ run __temporary_test.cpp test_instances//test_instances : : : <test-info>always_show_run_output <pch>off ]  
  
Will this PR be merged into boost 1.70? All autodiff-related tests were passing.

---

## Comment 67

> Username: jzmaddock  
> Created_at: 2019-02-28 18:31:22 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-468384572  

I think we're too late for major additions to 1.70 which is already into it's release-cycle.  I want to at least read this through properly before merge and I haven't found the time yet, but I will I promise.

---

## Comment 68

> Username: pulver  
> Created_at: 2019-02-28 18:42:43 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-468388477  

Ok thanks.

---

## Comment 69

> Username: NAThompson  
> Created_at: 2019-03-04 03:51:07 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-469109511  

On the `autodiff_fvar` verbosity: I think people will be just fine with it. However, Griewank uses the term "active" variable for structs of the form (x, \dot{x}), and passive variables for structs of the form (x, 0).  
  
Not sure whether this would be an improvement or not.

---

## Comment 70

> Username: pulver  
> Created_at: 2019-03-04 16:59:07 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-469329999  

> Griewank uses the term "active" variable for structs of the form (x, \dot{x}), and passive variables for structs of the form (x, 0).  
  
If I understand correctly, the initialization of active/passive variables corresponds to the 2-element constructor `fvar(const root_type& ca, const bool is_variable)` where `is_variable` is true/false, respectively. `make_fvar()` simply calls this constructor with `is_variable=true`.  
  
The translation between Griewank and autodiff is:  
  
* active variable = truncated taylor polynomial with some non-zero epsilon terms.  
* passive variable = truncated taylor polynomial with no epsilon terms. (aka *constant*).

---

## Comment 71

> Username: jzmaddock  
> Created_at: 2019-04-23 18:46:57 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-485927734  

I've created a new PR for integration testing, as I'm not sure why there are failures in the CI for this one (needs to have develop merged in?), but see https://github.com/boostorg/math/pull/198  
  
The main comments I have are documentation related:  
  
* It would be good to have an example of a function of 2 or more variables where only one is being differentiated.  The "obvious" use of a double for the non-differentiated variable seems to work OK, but it would be nice to have this officially blessed.  
* This seems like a good example where the use of auto for retuned results is a very good idea and should perhaps be encouraged in the docs?  
* It would help a lot to have an "extender manual" for functions which are better handled analytically.  Something like tgamma might make a good example.  
* It would help to know what operations are *not* supported - for example I tried feeding an audodiff variable into cyl_bessel_j, but that failed as one branch in the code requires a conversion from float->integer via boost::math::iround.  This may have been a lost cause anyway?  But I wonder if such conversions can be supported or not, as they crop up fairly commonly in special function code.  
  
None of these are showstoppers to merging to develop, but I do want to check the CI is green first.

---

## Comment 72

> Username: kedarbhat  
> Created_at: 2019-04-23 21:14:31 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-485977429  

> I've created a new PR for integration testing, as I'm not sure why there are failures in the CI for this one (needs to have develop merged in?), but see #198  
>   
> The main comments I have are documentation related:  
>   
> * It would be good to have an example of a function of 2 or more variables where only one is being differentiated.  The "obvious" use of a double for the non-differentiated variable seems to work OK, but it would be nice to have this officially blessed.  
> * This seems like a good example where the use of auto for retuned results is a very good idea and should perhaps be encouraged in the docs?  
> * It would help a lot to have an "extender manual" for functions which are better handled analytically.  Something like tgamma might make a good example.  
> * It would help to know what operations are _not_ supported - for example I tried feeding an audodiff variable into cyl_bessel_j, but that failed as one branch in the code requires a conversion from float->integer via boost::math::iround.  This may have been a lost cause anyway?  But I wonder if such conversions can be supported or not, as they crop up fairly commonly in special function code.  
>   
> None of these are showstoppers to merging to develop, but I do want to check the CI is green first.  
  
Hi John,   
  
I've seen the `float` -> `integer` issue pop up on MSVC builds specifically (though it's a general type conversion issue to be addressed); it's on my todo list, but I can prioritize it to see what's going on.

---

## Comment 73

> Username: pulver  
> Created_at: 2019-04-26 15:06:48 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-487091105  

> The main comments I have are documentation related:  
>   
> * It would be good to have an example of a function of 2 or more variables where only one is being differentiated.  The "obvious" use of a double for the non-differentiated variable seems to work OK, but it would be nice to have this officially blessed.  
>   
> * This seems like a good example where the use of auto for retuned results is a very good idea and should perhaps be encouraged in the docs?  
>   
> * It would help a lot to have an "extender manual" for functions which are better handled analytically.  Something like tgamma might make a good example.  
  
Thanks for the feedback. I'll submit a PR for these 3 points in the coming weeks.  
  
>   
> * It would help to know what operations are _not_ supported - for example I tried feeding an audodiff variable into cyl_bessel_j, but that failed as one branch in the code requires a conversion from float->integer via boost::math::iround.  This may have been a lost cause anyway?  But I wonder if such conversions can be supported or not, as they crop up fairly commonly in special function code.  
  
@kedarbhat has been doing looking deeply into this, and @shaneasd wrote out a list of incompatible functions [here](https://github.com/pulver/autodiff/issues/17#issuecomment-477271379). We'll come back with a more detailed list.  
  
Thanks for the merge!

---

## Comment 74

> Username: NAThompson  
> Created_at: 2019-04-26 16:33:43 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-487120199  

@pulver: Now that you've successfully pushed this past the finish line (a heroic effort, IMO), are you gonna start on reverse mode?

---

## Comment 75

> Username: pulver  
> Created_at: 2019-04-26 17:04:31 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-487129572  

> @pulver: Now that you've successfully pushed this past the finish line (a heroic effort, IMO), are you gonna start on reverse mode?  
  
Thanks! As mentioned [earlier](https://github.com/pulver/autodiff/issues/1#issuecomment-449673653), I'd like to get some feedback first on this, where peoples' pain points are, and incorporate the feedback into a future reverse mode version. But now I am a lot busier than I was when I wrote the forward mode, so it is hard to estimate when. However in the more immediate future, there are some new features coming, including declaration of multiple independent variables into a tuple, as this is somewhat clumsy right now having to manually specify the different dimensions for each independent variable. There's also generalized internal helper functions for calculating accurate derivatives for multi-variable library functions, such as `pow(x,y)` and `tan2(y,x)`. There's also some thoughts on improving the current `O(N*N)` performance of multiplication to `O(N*logN)` via the convolution theorem/FFT, depending on peoples' needs and feedback...

---

## Comment 76

> Username: kedarbhat  
> Created_at: 2019-05-01 21:32:53 UTC  
> Url: https://github.com/boostorg/math/pull/176#issuecomment-488438513  

> > The main comments I have are documentation related:  
> >   
> > * It would be good to have an example of a function of 2 or more variables where only one is being differentiated.  The "obvious" use of a double for the non-differentiated variable seems to work OK, but it would be nice to have this officially blessed.  
> > * This seems like a good example where the use of auto for retuned results is a very good idea and should perhaps be encouraged in the docs?  
> > * It would help a lot to have an "extender manual" for functions which are better handled analytically.  Something like tgamma might make a good example.  
>   
> Thanks for the feedback. I'll submit a PR for these 3 points in the coming weeks.  
>   
> > * It would help to know what operations are _not_ supported - for example I tried feeding an audodiff variable into cyl_bessel_j, but that failed as one branch in the code requires a conversion from float->integer via boost::math::iround.  This may have been a lost cause anyway?  But I wonder if such conversions can be supported or not, as they crop up fairly commonly in special function code.  
>   
> @kedarbhat has been doing looking deeply into this, and @shaneasd wrote out a list of incompatible functions [here](https://github.com/pulver/autodiff/issues/17#issuecomment-477271379). We'll come back with a more detailed list.  
>   
> Thanks for the merge!  
  
I have type conversions working now (under /Wall /permissive-).

---
