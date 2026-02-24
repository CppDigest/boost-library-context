# #269 Initial commit of pretty-formatter. [Open]

> Username: jzmaddock  
> Created at: 2019-11-06 19:33:05 UTC  
> Updated at: 2022-10-29 17:13:14 UTC  
> Url: https://github.com/boostorg/math/pull/269  

This still needs lots of work, but is starting to look useful, initial commit posted here for discussion.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-11-10 16:14:13 UTC  
> Updated_at: 2019-11-10 16:31:55 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-552209229  

Very cool; just went through the docs and got rid of a couple trivial typos.  
  
I'm not sure that this is high-quality feedback, because I'm so out of touch with text streaming, but looking through the API synopsis didn't give me much info on how to use the class. I'm wondering if a super basic a MWE front and center in the docs would be useful? (This is somewhat taken care of by the examples at the end.)  
  
(Also, forgot to add `[CI SKIP]`; that build can be cancelled.)  
  
Also, any plans for unicode subscripts and superscripts? Might be required for pretty-printing polynomials. Also it would make the `text_output` very useful for debugging: imagine the Chebyshev transform `ostream` operator spitting out a reasonably formatted Chebyshev series with unicode subscripts and superscripts. Other overloads for the `ostream` operator that could be really useful are the `barycentric_rational` interpolator, the `cardinal_cubic_b_spline` interpolator, the trigonometric interpolator, and the polynomials.  
  
Got perhaps a good warning from `g++-9`:  
  
```  
$ make example/formatter_text_output.x  
../../boost/math/tools/formatting.hpp:134:91: warning: 'strlen' argument missing terminating nul [-Wstringop-overflow=]  
  134 |                typename std::basic_string<charT, Traits>::size_type pos = s.find_first_of(exponent_string);  
      |                                                                                           ^~~~~~~~~~~~~~~  
../../boost/math/tools/formatting.hpp:128:35: note: referenced argument declared here  
  128 |                static const charT exponent_string[2] = { 'e', 'E' };  
      |                                   ^~~~~~~~~~~~~~~  
```  
  
Next, ran it with `-fsanitize=address -fsanitize=undefined`:  
  
```  
../../boost/math/tools/formatting.hpp:160:23: runtime error: load of value 1651076143, which is not a valid value for type 'imaginary_i_t'  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-11-10 17:00:04 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-552212936  

>Very cool; just went through the docs and got rid of a couple trivial typos.  
  
Thanks!  
  
>I'm not sure that this is high-quality feedback, because I'm so out of touch with text streaming, but looking through the API synopsis didn't give me much info on how to use the class. I'm wondering if a super basic a MWE front and center in the docs would be useful? (This is somewhat taken care of by the examples at the end.)  
  
Yes for sure, I just haven't got there yet.  But basically, almost whatever you can do to an ostream, you can do to a pretty printer instance.  
  
>Also, any plans for unicode subscripts and superscripts? Might be required for pretty-printing polynomials. Also it would make the text_output very useful for debugging: imagine the Chebyshev transform ostream operator spitting out a reasonably formatted Chebyshev series with unicode subscripts and superscripts. Other overloads for the ostream operator that could be really useful are the barycentric_rational interpolator, the cardinal_cubic_b_spline interpolator, the trigonometric interpolator, and the polynomials.  
  
I confess I hadn't realised Unicode could do that - I need to look into that, and yes spit out UTF8.  
  
> Got perhaps a good warning from g++-9  
  
Yup, a bug, will fix.  
  
Working on polynomials now BTW: I suspect as I go along many internals will get re-written somewhat.  At the moment this is more of a sketch of an idea than fully fledged, but I think it's getting there...

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-11-13 18:37:26 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-553541663  

Latest version now supports polynomial types plus Unicode plain text output (though still some things to double check).  
  
Chebyshev transform I can see how to support, but the barycentric_rational, cardinal_cubic_b_spline and trigonometric interpolators I don't see what should be printed?

---

## Comment 4

> Username: NAThompson  
> Created_at: 2019-11-14 16:41:13 UTC  
> Updated_at: 2019-11-14 16:42:23 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-553972358  

> Latest version now supports polynomial types plus Unicode plain text output (though still some things to double check).  
  
Just ran it on my machine; looks great! BTW, the docs don't show how to initialize the polynomial (`example/formatting_snips.cpp` excises the initialization); was this intentional?  
  
Barycentric rational might be harder than I had thought, the sum over a_k/(x-x_k) really looks quite challenging without LaTeX support.  
  
The trigonometric interpolator might be able to work just like the polynomial; just take z = e^{i\theta} and don't bother to expand it into sines and cosines. That's the easy way, since the internal rep is a complex polynomial. Maybe it'd be useful to spit out sines and cosines as well.  
  
Cubic B-spline: 3.4B₃(0.01(x-1)) + 8.7B₃(0.01(x-2)) +   
  
I'll try to take a look at these.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2019-11-14 17:44:41 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-553999528  

BTW, the unicode superscripts for n>=10 look a little bizarre on my console, but perfectly fine on Firefox:  
  
```  
 ./main.x  
(2.25 - 3.5i) - (12.5 + 4.5i)x + 23.34x² + 34.5ix³ + (2.25 - 3.5i)x⁴ - (12.5 + 4.5i)x⁵ + 23.34x⁶ + 34.5ix⁶ + (2.25 - 3.5i)x⁸ - (12.5 + 4.5i)x⁹ + 23.34x¹⁰ + 34.5ix¹¹ + (2.25 - 3.5i)x¹² - (12.5 + 4.5i)x¹³ + 23.34x¹⁴ + 34.5ix¹⁵ + (2.25 - 3.5i)x¹⁶ - (12.5 + 4.5i)x¹⁶ + 23.34x¹⁸ + 34.5ix¹⁹ + (2.25 - 3.5i)x²⁰ - (12.5 + 4.5i)x²¹ + 23.34x²² + 34.5ix²³  
```  
  
How do they look on windows terminal?

---

## Comment 6

> Username: pabristow  
> Created_at: 2019-11-15 09:31:51 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554284272  

Every app I have tried has worked except Notepad++ where I suspect that a font change will make the superscripts about 3 work rather than a box.  All browsers, edge Chrome Firefox, Opera, Word, Excel, Outllok Onenote, and Textpad all work fine.  
  
So -  very pretty!

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2019-11-16 18:18:14 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554661384  

On Linux I see the superscripts are rather spaced out, likewise on Cygwin:  
  
![cygwin](https://user-images.githubusercontent.com/5011768/68997318-f5cd1500-089c-11ea-91db-8417562b17a6.png)  
  
I believe this is an artefact of the console using a fixed width font, so although the number are shrunk down in size, their spacing remains the same.  Firefox is likely using a regular variable width font and putting the superscripted numbers closers together.  
  
MSVC console is a whole other beast, and doesn't really support Unicode out (at least not be default).

---

## Comment 8

> Username: NAThompson  
> Created_at: 2019-11-16 18:45:12 UTC  
> Updated_at: 2019-11-16 18:48:08 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554663494  

Yup you're right: It's a monospaced font problem.  
  
Quick question: The docs show a zero before the 6 on the polynomial formatting, namely 10^-06 rather than 10^-6, is this intended?  
  
![polynomial_format](https://user-images.githubusercontent.com/5459618/68997695-43cf2200-0877-11ea-9edf-2a9f2f5789a3.png)  
  
I'm also getting a bizarre tilde spat out after each infinity on `zsh`:  
  
![bizarre_tilde](https://user-images.githubusercontent.com/5459618/68997737-94467f80-0877-11ea-8a50-bfaa5ab40a88.png)  
  
(I think this might be a bug in the console; when I highlighted it to copy-past, it disappeared. I think we might discover a lot of unicode-related bugs with this.)

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2019-11-16 19:41:16 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554667883  

>Quick question: The docs show a zero before the 6 on the polynomial formatting, namely 10^-06 rather than 10^-6, is this intended?  
  
Sort of: the intention is to not mess with the underlying iostreams formatting, I could strip leading zeros from the exponent if it's going to look better (I suspect it will).  
  
>I'm also getting a bizarre tilde spat out after each infinity on zsh:  
  
It's supposed to be a complex-infinity: the infinity symbol with a tilde above, so it uses the combining mark to produce that.  Cygwin mucks it up s well:  
  
![cygwin2](https://user-images.githubusercontent.com/5011768/68998283-557ced80-08a8-11ea-89aa-4d430ca63f58.png)  
  
As do other text editors, so maybe this wasn't such a hot idea :(  
  
HTML renders the same Unicode sequence correctly though:  
  
![html](https://user-images.githubusercontent.com/5011768/68998345-f53a7b80-08a8-11ea-88cb-6bf5694dd11d.png)

---

## Comment 10

> Username: NAThompson  
> Created_at: 2019-11-16 20:22:24 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554671022  

How about \mathbb{C}(\infty) for complex infinity? There is also a [unicode blackboard bold](https://en.wikipedia.org/wiki/Blackboard_bold). (Is there any end to the scope of unicode?)

---

## Comment 11

> Username: pabristow  
> Created_at: 2019-11-17 09:55:39 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554729950  

Similarly mingw and git bash (MS DOSbox).  I didn't try this assuming (correctly!) it would not work.  
  
This would work OK if the Unicode page provided a full set of superscript numbers 0 to 9.  0 to 3 are done differently, so on all these systems, the numers 4 to 9 don't display - you get a box or something instead.    
  
"  
The most common superscript digits (1, 2, and 3) were in ISO-8859-1 and were therefore carried over into those positions in the Latin-1 range of Unicode.  
"  
Tough!  
  
PS monospacing  means the display is as expected - less pretty.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2019-11-17 16:39:51 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554763554  

>How about \mathbb{C}(\infty) for complex infinity?  
  
That looks dangerously close to the Riemann sphere as in https://en.wikipedia.org/wiki/Riemann_sphere.  The same text just uses \infty for complex-infinity, so maybe this should do the same for plain text output?  Putting a tilde on top is a Mathematica-ism BTW, I haven't been able to find any "official" guidance on how complex infinity should be typeset.  
  
> Similarly mingw and git bash (MS DOSbox).   
  
There is supposed to be a way to make this work on a Windows console - cygwin manages it after all, it's "just" a question of getting the right code-page loaded.

---

## Comment 13

> Username: NAThompson  
> Created_at: 2019-11-17 17:32:03 UTC  
> Updated_at: 2019-11-17 17:39:04 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554767929  

> That looks dangerously close to the Riemann sphere  
  
Yup, that's the goal! I remember my complex analysis class demonstrating the automorphism between the sphere and the plane; the takeaway being "there is only one complex infinity".  
  
>  The same text just uses \infty for complex-infinity, so maybe this should do the same for plain text output?   
  
I think that is a perfectly sensible option. I actually like Mathematica's notation here; sadly non-LaTeX mathematical text rendering is still in the stone-age.  
  
In any case, the more I think about it, the more I feel like pretty-printing is a hugely important task for the library. For instance, reading through the docs to find out what scaling convention is used for some object is actually pretty challenging; way easier just to print it.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2019-11-17 18:08:21 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554770930  

@pabristow : Printing to the windows console works (mostly) fine if you add:  
  
```  
SetConsoleOutputCP(CP_UTF8);  
```  
  
At the start of main.  There are a couple of (seldom used) symbols that seem to be unsupported - maybe this is fixable by changing the console font (or not), but all the defaults work fine including all the super/sub scripts.

---

## Comment 15

> Username: NAThompson  
> Created_at: 2019-11-17 18:26:50 UTC  
> Updated_at: 2019-11-17 18:30:32 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554772542  

I've managed to get a really bush-league implementation of the pretty-printer working for the `cardinal_cubic_b_spline`, but I can't quite see how you're avoiding adding code directly into the classes and instead only using the `tools/formatting.hpp` file; I needed a friend declaration to access the private members.  
  
In any case, here's the code:  
  
`boost/math/interpolators/detail/cardinal_cubic_b_spline_detail.hpp`:  
  
```cpp  
    friend std::ostream& operator<<(std::ostream& os, const cardinal_cubic_b_spline_imp<Real>& spline) {  
        os << spline.m_avg << "+";  
        for (int64_t k = 0; k < int64_t(spline.m_beta.size()); ++k) {  
            os << spline.m_beta[k] << "×B₃(" << spline.m_h_inv << "(x-" << spline.m_a << ") + " << 1 - k << ") + ";  
        }  
        return os;  
    }  
```  
  
(Obviously haven't implemented the lookahead to see if I should put a `+` or `-` in front of each summand.)  
  
`boost/math/interpolators/cardinal_cubic_b_spline.hpp`:  
  
```cpp  
    friend std::ostream& operator<<(std::ostream& os, const cardinal_cubic_b_spline<Real>& spline) {  
        os << *spline.m_imp;  
        return os;  
    }  
```  
  
Currently looks like this:  
  
```  
32+-33×B₃(10(x-5) + 1) + -27×B₃(10(x-5) + 0) + -21×B₃(10(x-5) + -1) + -15×B₃(10(x-5) + -2) + -9×B₃(10(x-5) + -3) + -3×B₃(10(x-5) + -4) + 3×B₃(10(x-5) + -5) + 9×B₃(10(x-5) + -6) + 15×B₃(10(x-5) + -7) + 21×B₃(10(x-5) + -8) + 27×B₃(10(x-5) + -9) + 33×B₃(10(x-5) + -10) + %    
```  
  
but I hope to get it to:  
  
```  
32 - 33×B₃(10(x-5) + 1) - 27×B₃(10(x-5)) - 21×B₃(10(x-5) - 1) - 15×B₃(10(x-5) - 2) - 9×B₃(10(x-5) - 3) - 3×B₃(10(x-5) - 4) + 3×B₃(10(x-5) - 5) + 9×B₃(10(x-5) - 6) + 15×B₃(10(x-5) - 7) + 21×B₃(10(x-5) - 8) + 27×B₃(10(x-5) - 9) + 33×B₃(10(x-5) - 10)  
```  
  
Test program:  
  
`main.cpp`:  
```cpp  
#include <iostream>  
#include <vector>  
#include <boost/math/interpolators/cardinal_cubic_b_spline.hpp>  
  
int main() {  
    std::vector<double> v(10);  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = 5 + 6*i;  
    }  
  
    double step = 0.1;  
    double a = 5;  
    boost::math::interpolators::cardinal_cubic_b_spline<double> spline(v.data(), v.size(), a, step);  
    std::cout << spline;  
}  
```

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2019-11-17 19:17:01 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554776789  

> I've managed to get a really bush-league implementation of the pretty-printer working for the cardinal_cubic_b_spline, but I can't quite see how you're avoiding adding code directly into the classes and instead only using the tools/formatting.hpp file.  
  
The issue here is that formatter.hpp relies on public API's of the types being formatted, but the interpolators don't expose their internals.  Conversely, the formatters have some very ad-hoc code in them - which is fine if you just want to format the basic number types - but types which are really general purpose expressions then get a lot more difficult.    
  
So I guess the question is whether the formatters should be re-worked into something more like an expression-printer complete with symbolic representations of arbitrary expressions?  
  
It's certainly do-able, probably very useful and logical, but I confess way more work than I thought I was taking on ;)

---

## Comment 17

> Username: NAThompson  
> Created_at: 2019-11-17 19:43:47 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554779046  

> So I guess the question is whether the formatters should be re-worked into something more like an expression-printer complete with symbolic representations of arbitrary expressions?  
  
Would it be less work to just do a collection of one-offs for each interpolator/object we want to print? Obviously it's not ideal but nonetheless it could still work if we all decided to build this expertise together.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2019-11-18 08:46:05 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554913580  

>Would it be less work to just do a collection of one-offs for each interpolator/object we want to print?   
  
I think so.... what if I do a bit of refactoring and expose a public API for printing various symbols plus super/subscripts etc?  Then outputting a "special" type would just be a sequence of function calls to format the various components?

---

## Comment 19

> Username: pabristow  
> Created_at: 2019-11-18 10:22:42 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-554952020  

I also note that the code page can be set in the registry (65001 for UTF-8) but I haven't dared try this yet. Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\CodePage  
  
May have other side effects?

---

## Comment 20

> Username: NAThompson  
> Created_at: 2019-11-18 13:06:19 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-555006767  

> what if I do a bit of refactoring and expose a public API for printing various symbols plus super/subscripts etc?  
  
I like this idea; and will test it out with the B-spline once it's ready.

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2019-11-23 19:37:21 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-557826719  

OK docs for the formatting API were done in a tad of a hurry, but the basics are all there I hope.  
  
Implemented cubic B spline printing using the API (but feel free to change).  
  
Plain text output:  
  
32 - 33×B₃(10(x - 5) + 1) - 27×B₃(10(x - 5)) - 21×B₃(10(x - 5) - 1) - 15×B₃(10(x - 5) - 2) - 9×B₃(10(x - 5) - 3) - 3×B₃(10(x - 5) - 4) + 3×B₃(10(x - 5) - 5) + 9×B₃(10(x - 5) - 6) + 15×B₃(10(x - 5) - 7) + 21×B₃(10(x - 5) - 8) + 27×B₃(10(x - 5) - 9) + 33×B₃(10(x - 5) - 10)  
  
HTML output:  
  
<span class="number"><span class="float">32 - <span class="number"><span class="float">33&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) + <span class="number"><span class="integer">1) - <span class="number"><span class="float">27&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5)) - <span class="number"><span class="float">21&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">1) - <span class="number"><span class="float">15&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">2) - <span class="number"><span class="float">9&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">3) - <span class="number"><span class="float">3&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">4) + <span class="number"><span class="float">3&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">5) + <span class="number"><span class="float">9&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">6) + <span class="number"><span class="float">15&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">7) + <span class="number"><span class="float">21&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">8) + <span class="number"><span class="float">27&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">9) + <span class="number"><span class="float">33&#xd7;B<sub>3</sub>(<span class="number"><span class="float">10(<I>x</I> - <span class="number"><span class="float">5) - <span class="number"><span class="integer">10)

---

## Comment 22

> Username: NAThompson  
> Created_at: 2019-11-23 23:42:49 UTC  
> Updated_at: 2019-11-24 00:22:21 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-557843146  

Looks awesome! One quick comment with the B-spline: Do you think it would like better with your 'cdot', namely:  
  
32 - 33·B₃(10(x - 5) + 1) - 27·B₃(10(x - 5)) - 21·B₃(10(x - 5) - 1) - 15·B₃(10(x - 5) - 2) - 9·B₃(10(x - 5) - 3) ...  
  
Also, just ran it on Mac, read through the docs, built the examples with sanitizers; no problems on my end.  
  
  
Also, not sure if this is something to worry about, but when compiling with sanitizers it seems to take quite a while:  
  
```  
$ time make DEBUG=1 example/formatter_text_output.x  
g++-9 -g --std=c++17 -ffast-math -march=native -Wfatal-errors -MMD -fvisibility=hidden -O2 -fsanitize=address -fsanitize=undefined -I../../ -I/usr/local/include -o example/formatter_text_output.x example/formatter_text_output.cpp -pthread -L/usr/local/lib  
52.41s user 2.24s system 77% cpu 1:10.83 total  
```  
  
Without the sanitizers it take ~11 seconds, which seems fine.

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2019-11-24 08:44:09 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-557868923  

>One quick comment with the B-spline: Do you think it would like better with your 'cdot', namely  
  
Perhaps... but we need to pick one as the default, after that the user can choose which they prefer by using the manipulators?  
  
>Also, not sure if this is something to worry about, but when compiling with sanitizers it seems to take quite a while  
  
Yikes, that is a long time!  The file does instantiate a heck of a lot of stuff though, including some multiprecision types.  However if I restrict it to integer and floating point types, and remove the multiprecision stuff, the compile time drops to ~ 1s for me even with the sanitizers on.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2019-11-24 09:23:59 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-557871485  

The long compile time is a gcc artifact, I see:  
  
```  
time g++-9 -g --std=c++17  -Wfatal-errors -fvisibility=hidden -O2 -fsanitize=address -fsanitize=undefined -I../../..  formatter_text_output.cpp -pthread  
formatter_text_output.cpp: In function ‘void print(std::basic_ostream<charT>&) [with charT = char]’:  
formatter_text_output.cpp:14:6: note: variable tracking size limit exceeded with ‘-fvar-tracking-assignments’, retrying without  
   14 | void print(std::basic_ostream<charT>& os)  
      |      ^~~~~  
  
real	0m40.283s  
user	0m39.380s  
sys	0m0.794s  
```  
  
But if I remove the -O2 -ffast-math I get:  
  
```  
time g++-9 -g --std=c++17  -Wfatal-errors -fvisibility=hidden  -fsanitize=address -fsanitize=undefined -I../../..  formatter_text_output.cpp -pthread  
  
real	0m6.639s  
user	0m6.307s  
sys	0m0.310s  
```  
  
Things are similarly improved if I remove the -g but keep the optimizations, or indeed if I just use clang.

---

## Comment 25

> Username: NAThompson  
> Created_at: 2019-11-24 15:58:06 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-557901783  

> Perhaps... but we need to pick one as the default, after that the user can choose which they prefer by using the manipulators?  
  
Yeah, just looking at it again makes me realize that this is less of an unambiguous win than I thought it was. Sorry for the bikeshedding; keep the eye on the prize: This allows users to easily verify that their mental representation of their object is the same as ours.  
  
> Things are similarly improved if I remove the -g but keep the optimizations, or indeed if I just use clang.  
  
Yeah, `gcc`'s sanitizer suite is not quite as good as clangs. Dunno if it's worth a bugzilla or not . . .

---

## Comment 26

> Username: NAThompson  
> Created_at: 2019-11-30 22:24:10 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560026128  

Ugh looks like you’ve probably uncovered a bug in the quadratic and quintic B spline interpolators, it’s spitting out nans

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2019-12-01 11:12:56 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560093615  

>Ugh looks like you’ve probably uncovered a bug in the quadratic and quintic B spline interpolators, it’s spitting out nans  
  
The (very basic) example is OK for me, or is this with something else?  
  
BTW can you check that I've documented the splines correctly - particularly how many points they include when interpolating?  Thanks!

---

## Comment 28

> Username: NAThompson  
> Created_at: 2019-12-01 14:38:11 UTC  
> Updated_at: 2019-12-01 14:50:07 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560115663  

Here's what I get on Mac OS, zsh:  
  
```  
➜  math git:(formatting) ✗ make example/formatter_b_spline_output.x  
g++-9 -g --std=c++17 -ffast-math -march=native -Wfatal-errors -MMD -fvisibility=hidden -O3 -I../../ -I/usr/local/include -o example/formatter_b_spline_output.x example/formatter_b_spline_output.cpp -pthread  -L/usr/local/lib  
➜  math git:(formatting) ✗ ./example/formatter_b_spline_output.x  
Quadratic B Spline:  
  
nan×B₂(10(x - 5) + 1) + nan×B₂(10(x - 5)) + nan×B₂(10(x - 5) - 1) + nan×B₂(10(x - 5) - 2) + nan×B₂(10(x - 5) - 3) + nan×B₂(10(x - 5) - 4) + nan×B₂(10(x - 5) - 5) + nan×B₂(10(x - 5) - 6) + nan×B₂(10(x - 5) - 7) + nan×B₂(10(x - 5) - 8) + nan×B₂(10(x - 5) - 9) + nan×B₂(10(x - 5) - 10)  
  
$nan\times\mathrm{B}_{2}(10(x - 5) + 1) + nan\times\mathrm{B}_{2}(10(x - 5)) + nan\times\mathrm{B}_{2}(10(x - 5) - 1) + nan\times\mathrm{B}_{2}(10(x - 5) - 2) + nan\times\mathrm{B}_{2}(10(x - 5) - 3) + nan\times\mathrm{B}_{2}(10(x - 5) - 4) + nan\times\mathrm{B}_{2}(10(x - 5) - 5) + nan\times\mathrm{B}_{2}(10(x - 5) - 6) + nan\times\mathrm{B}_{2}(10(x - 5) - 7) + nan\times\mathrm{B}_{2}(10(x - 5) - 8) + nan\times\mathrm{B}_{2}(10(x - 5) - 9) + nan\times\mathrm{B}_{2}(10(x - 5) - 10)$  
  
<span class="number"><span class="float">nan</span></span>&#xd7;B<sub>2</sub>(<span class="number"><span class="float">10</span></span>(<I>x</I> - <span class="number"><span class="float">5</span></span>) + <span class="number"><span class="integer">1</span></span>) + <span class="number"><span class="float">nan</span></span>&#xd7;B<sub>2</sub>(<span class="number"><span class="float">10</span></span>(<I>x</I> - <span class="number"><span class="float">5</span></span>)) + <span class="number"><span class="float">nan</span></span>&#xd7;B<sub>2</sub>(<span class="number"><span class="float">10</span></span>(<I>x</I> - <span class="number"><span class="float">5</span></span>) - <span c ...  
```  
  
Same thing with the quintic B-spline.  
  
Update: I figured this one out, my fault: It's the `-ffast-math`; the B-splines use `nan` to indicate that the user wants to estimate the derivatives. So I added `-ffast-math -fno-finite-math-only` and it was fine.

---

## Comment 29

> Username: NAThompson  
> Created_at: 2019-12-01 14:44:23 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560116304  

Your comment is:  
  
> Formatted output of this type is primarily for debugging purposes since only 4 consecutive values of the sum are used for any given abscissa value, where as the printed form shows all the value of the sum.   
  
I would state it differently: "Despite appearances, only a few terms of the sum are nonzero, since the basis function B_n has compact support."  
  
The actual number of non-zero terms for a given abscissa is fairly complicated to derive.

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2019-12-01 17:54:09 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560136931  

Hmm, on cygwin I see:  
  
-B₂(10(x - 5) + 1) + 5×B₂(10(x - 5)) + 11×B₂(10(x - 5) - 1) + 17×B₂(10(x - 5) - 2) + 23×B₂(10(x - 5) - 3) + 29×B₂(10(x - 5) - 4) + 35×B₂(10(x - 5) - 5) + 41×B₂(10(x - 5) - 6) + 47×B₂(10(x - 5) - 7) + 53×B₂(10(x - 5) - 8) + 59×B₂(10(x - 5) - 9) + 65×B₂(10(x - 5) - 10)  
  
For the quartic, and for the quintic:  
  
-7×B₅(10(x - 5) + 2) - B₅(10(x - 5) + 3) + 5×B₅(10(x - 5) + 4) + 11×B₅(10(x - 5) + 5) + 17×B₅(10(x - 5) + 6) + 23×B₅(10(x - 5) + 7) + 29×B₅(10(x - 5) + 8) + 35×B₅(10(x - 5) + 9) + 41×B₅(10(x - 5) + 10) + 47×B₅(10(x - 5) + 11) + 53×B₅(10(x - 5) + 12) + 59×B₅(10(x - 5) + 13) + 65×B₅(10(x - 5) + 14) + 71×B₅(10(x - 5) + 15)

---

## Comment 31

> Username: jzmaddock  
> Created_at: 2019-12-01 18:00:53 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560137639  

The only difference I see with the quadratic and quintic B spline is that they use std::isnan rather than boost::math::isnan.  It really shouldn't make any difference, but just in case I've pushed a fix to prevent macro expansion of isnan.  
  
Wild idea: are you using -ffast-math?  I wonder if the compiler is optimising the call away (assuming it's seeing "x != x" or similar) and so isnan is always false?

---

## Comment 32

> Username: jzmaddock  
> Created_at: 2019-12-01 19:54:29 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560151489  

Barycentric rational support added - it's a bit verbose and hard to read unless you use LaTex output, but it does work.  
  
Plain text:  
  
P⁽ˣ⁾⁄Q₍ₓ₎ ; P(x) = ⁵⋅⁶²⁶×⁻²⁰⁸³³⋅³⁄₍ₓ ₋ ₀.₀₂₎ + ⁵⋅⁵⁴⁴×⁸³³³³⋅³⁄₍ₓ ₋ ₀.₀₄₎ + ⁵⋅⁴⁵×⁻¹⁴⁵⁸³³⁄₍ₓ ₋ ₀.₀₆₎ + ⁵⋅³⁵¹×¹⁶⁶⁶⁶⁶⁄₍ₓ ₋ ₀.₀₈₎ + ⁵⋅²⁵³×⁻¹⁶⁶⁶⁶⁶⁄₍ₓ ₋ ₀.₁₎ + ⁵⋅¹⁵⁶×¹⁶⁶⁶⁶⁶⁄₍ₓ ₋ ₀.₁₂₎ + ⁵⋅⁰⁵⁸×⁻¹⁶⁶⁶⁶⁶⁄₍ₓ ₋ ₀.₁₄₎ + ⁴⋅⁹⁶×¹⁶¹⁴⁵⁸⁄₍ₓ ₋ ₀.₁₆₎ + ⁴⋅⁸⁶²×⁻¹³³³³³⁄₍ₓ ₋ ₀.₁₈₎ + ⁴⋅⁶⁶²×⁶⁰³¹²⋅⁵⁄₍ₓ ₋ ₀.₂₎ + ⁴⋅⁵⁶³×⁻²⁶⁰⁴¹⋅⁶⁄₍ₓ ₋ ₀.₂₄₎ + ⁴⋅³⁶×²¹³⁵⁴⋅²⁄₍ₓ ₋ ₀.₂₈₎ + ⁴⋅¹⁵⁸⁴×⁻²⁰⁸³³⋅³⁄₍ₓ ₋ ₀.₃₂₎ + ³⋅⁹⁴⁶³×²⁰⁸³³⋅³⁄₍ₓ ₋ ₀.₃₆₎ + ³⋅⁶³⁶×⁻²⁰⁸³³⋅³⁄₍ₓ ₋ ₀.₄₎ + ³⋅⁵⁴²⁹×²⁰⁸³³⋅³⁄₍ₓ ₋ ₀.₄₄₎ + ³⋅³⁶⁹⁶×⁻²⁰⁸³³⋅³⁄₍ₓ ₋ ₀.₄₈₎ + ³⋅²⁴¹⁶×²⁰¹⁸²⋅³⁄₍ₓ ₋ ₀.₅₂₎ + ³⋅¹²⁰⁹×⁻¹⁶⁶⁶⁶⋅⁶⁄₍ₓ ₋ ₀.₅₆₎ + ³⋅⁰¹³⁸×⁸⁶⁸⁹⋅⁰⁶⁄₍ₓ ₋ ₀.₆₎ + ²⋅⁸³⁴²×⁻³²⁵⁵⋅²¹⁄₍ₓ ₋ ₀.₆₈₎ + ²⋅⁶⁸⁸¹×²⁶⁶⁹⋅²⁶⁄₍ₓ ₋ ₀.₇₆₎ + ²⋅⁵⁶⁶²×⁻²⁶⁰⁴⋅¹⁶⁄₍ₓ ₋ ₀.₈₄₎ + ²⋅⁴²⁴²×²⁶⁰⁴⋅¹⁶⁄₍ₓ ₋ ₀.₉₂₎ + ²⋅³⁶⁶⁶×⁻²⁶⁰⁴⋅¹⁶⁄₍ₓ ₋ ₁₎ + ²⋅³⁰⁵⁸×²⁶⁰⁴⋅¹⁶⁄₍ₓ ₋ ₁.₀₈₎ + ²⋅²⁴⁵⁸×⁻²⁶⁰⁴⋅¹⁶⁄₍ₓ ₋ ₁.₁₆₎ + ²⋅²⁰³⁵×²⁶⁰⁴⋅¹⁶⁄₍ₓ ₋ ₁.₂₄₎ + ²⋅¹⁶⁶¹×⁻²⁵²²⋅⁶⁹⁄₍ₓ ₋ ₁.₃₂₎ + ²⋅¹³⁵×²⁰⁸³⋅³³⁄₍ₓ ₋ ₁.₄₎ + ²⋅¹⁰⁹×⁻¹⁰⁹⁸⋅⁶³⁄₍ₓ ₋ ₁.₄₈₎ + ²⋅⁰⁶⁹⁶×⁴⁰⁶⋅⁹⁰¹⁄₍ₓ ₋ ₁.₆₄₎ + ²⋅⁰⁴⁶⁶×⁻³³³⋅⁶⁵⁹⁄₍ₓ ₋ ₁.₈₎ + ²⋅⁰³²⁵×³²⁵⋅⁵²¹⁄₍ₓ ₋ ₁.₉₆₎ + ²⋅⁰²⁸⁸×⁻³²⁵⋅⁵²¹⁄₍ₓ ₋ ₂.₁₂₎ + ²⋅⁰²⁹²×³²⁵⋅⁵²¹⁄₍ₓ ₋ ₂.₂₈₎ + ²⋅⁰²²⁸×⁻³²⁵⋅⁵²¹⁄₍ₓ ₋ ₂.₄₄₎ + ²⋅⁰¹²⁴×³²⁵⋅⁵²¹⁄₍ₓ ₋ ₂.₆₎ + ²⋅⁰⁰⁶⁵×⁻³²⁵⋅⁵²¹⁄₍ₓ ₋ ₂.₇₆₎ + ²⋅⁰⁰³¹×³²⁵⋅⁵²¹⁄₍ₓ ₋ ₂.₉₂₎ + ²⋅⁰⁰¹⁵×⁻³²⁵⋅⁵²¹⁄₍ₓ ₋ ₃.₀₈₎ + ²⋅⁰⁰⁰⁸×³²⁵⋅⁵²¹⁄₍ₓ ₋ ₃.₂₄₎ + ²⋅⁰⁰⁰⁴×⁻²⁸⁴⋅⁸³¹⁄₍ₓ ₋ ₃.₄₎ + ²⋅⁰⁰⁰²×¹⁶²⋅⁶⁶⁄₍ₓ ₋ ₃.₅₆₎ + ²⋅⁰⁰⁰¹×⁻⁴⁰⋅⁶⁹⁰¹⁄₍ₓ ₋ ₃.₇₂₎ ∧ Q(x) = ⁵⋅⁶²⁶⁄₍ₓ ₋ ₀.₀₂₎ + ⁵⋅⁵⁴⁴⁄₍ₓ ₋ ₀.₀₄₎ + ⁵⋅⁴⁵⁄₍ₓ ₋ ₀.₀₆₎ + ⁵⋅³⁵¹⁄₍ₓ ₋ ₀.₀₈₎ + ⁵⋅²⁵³⁄₍ₓ ₋ ₀.₁₎ + ⁵⋅¹⁵⁶⁄₍ₓ ₋ ₀.₁₂₎ + ⁵⋅⁰⁵⁸⁄₍ₓ ₋ ₀.₁₄₎ + ⁴⋅⁹⁶⁄₍ₓ ₋ ₀.₁₆₎ + ⁴⋅⁸⁶²⁄₍ₓ ₋ ₀.₁₈₎ + ⁴⋅⁶⁶²⁄₍ₓ ₋ ₀.₂₎ + ⁴⋅⁵⁶³⁄₍ₓ ₋ ₀.₂₄₎ + ⁴⋅³⁶⁄₍ₓ ₋ ₀.₂₈₎ + ⁴⋅¹⁵⁸⁴⁄₍ₓ ₋ ₀.₃₂₎ + ³⋅⁹⁴⁶³⁄₍ₓ ₋ ₀.₃₆₎ + ³⋅⁶³⁶⁄₍ₓ ₋ ₀.₄₎ + ³⋅⁵⁴²⁹⁄₍ₓ ₋ ₀.₄₄₎ + ³⋅³⁶⁹⁶⁄₍ₓ ₋ ₀.₄₈₎ + ³⋅²⁴¹⁶⁄₍ₓ ₋ ₀.₅₂₎ + ³⋅¹²⁰⁹⁄₍ₓ ₋ ₀.₅₆₎ + ³⋅⁰¹³⁸⁄₍ₓ ₋ ₀.₆₎ + ²⋅⁸³⁴²⁄₍ₓ ₋ ₀.₆₈₎ + ²⋅⁶⁸⁸¹⁄₍ₓ ₋ ₀.₇₆₎ + ²⋅⁵⁶⁶²⁄₍ₓ ₋ ₀.₈₄₎ + ²⋅⁴²⁴²⁄₍ₓ ₋ ₀.₉₂₎ + ²⋅³⁶⁶⁶⁄₍ₓ ₋ ₁₎ + ²⋅³⁰⁵⁸⁄₍ₓ ₋ ₁.₀₈₎ + ²⋅²⁴⁵⁸⁄₍ₓ ₋ ₁.₁₆₎ + ²⋅²⁰³⁵⁄₍ₓ ₋ ₁.₂₄₎ + ²⋅¹⁶⁶¹⁄₍ₓ ₋ ₁.₃₂₎ + ²⋅¹³⁵⁄₍ₓ ₋ ₁.₄₎ + ²⋅¹⁰⁹⁄₍ₓ ₋ ₁.₄₈₎ + ²⋅⁰⁶⁹⁶⁄₍ₓ ₋ ₁.₆₄₎ + ²⋅⁰⁴⁶⁶⁄₍ₓ ₋ ₁.₈₎ + ²⋅⁰³²⁵⁄₍ₓ ₋ ₁.₉₆₎ + ²⋅⁰²⁸⁸⁄₍ₓ ₋ ₂.₁₂₎ + ²⋅⁰²⁹²⁄₍ₓ ₋ ₂.₂₈₎ + ²⋅⁰²²⁸⁄₍ₓ ₋ ₂.₄₄₎ + ²⋅⁰¹²⁴⁄₍ₓ ₋ ₂.₆₎ + ²⋅⁰⁰⁶⁵⁄₍ₓ ₋ ₂.₇₆₎ + ²⋅⁰⁰³¹⁄₍ₓ ₋ ₂.₉₂₎ + ²⋅⁰⁰¹⁵⁄₍ₓ ₋ ₃.₀₈₎ + ²⋅⁰⁰⁰⁸⁄₍ₓ ₋ ₃.₂₄₎ + ²⋅⁰⁰⁰⁴⁄₍ₓ ₋ ₃.₄₎ + ²⋅⁰⁰⁰²⁄₍ₓ ₋ ₃.₅₆₎ + ²⋅⁰⁰⁰¹⁄₍ₓ ₋ ₃.₇₂₎  
  
HTML:  
  
<sup>P(<I>x</I>)</sup>&#x2044;<sub>Q(<I>x</I>)</sub> ; P(<I>x</I>) = <sup><span class="number"><span class="float">5.727</span></span>&#xd7;<span class="number"><span class="float">-20833.3</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.02</span></span>)</sub> + <sup><span class="number"><span class="float">5.544</span></span>&#xd7;<span class="number"><span class="float">83333.3</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.04</span></span>)</sub> + <sup><span class="number"><span class="float">5.45</span></span>&#xd7;<span class="number"><span class="float">-145833</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.06</span></span>)</sub> + <sup><span class="number"><span class="float">5.351</span></span>&#xd7;<span class="number"><span class="float">166667</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.08</span></span>)</sub> + <sup><span class="number"><span class="float">5.253</span></span>&#xd7;<span class="number"><span class="float">-166667</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.1</span></span>)</sub> + <sup><span class="number"><span class="float">5.157</span></span>&#xd7;<span class="number"><span class="float">166667</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.12</span></span>)</sub> + <sup><span class="number"><span class="float">5.058</span></span>&#xd7;<span class="number"><span class="float">-166667</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.14</span></span>)</sub> + <sup><span class="number"><span class="float">4.96</span></span>&#xd7;<span class="number"><span class="float">161458</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.16</span></span>)</sub> + <sup><span class="number"><span class="float">4.862</span></span>&#xd7;<span class="number"><span class="float">-133333</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.18</span></span>)</sub> + <sup><span class="number"><span class="float">4.762</span></span>&#xd7;<span class="number"><span class="float">70312.5</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.2</span></span>)</sub> + <sup><span class="number"><span class="float">4.563</span></span>&#xd7;<span class="number"><span class="float">-26041.7</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.24</span></span>)</sub> + <sup><span class="number"><span class="float">4.36</span></span>&#xd7;<span class="number"><span class="float">21354.2</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.28</span></span>)</sub> + <sup><span class="number"><span class="float">4.1584</span></span>&#xd7;<span class="number"><span class="float">-20833.3</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.32</span></span>)</sub> + <sup><span class="number"><span class="float">3.9463</span></span>&#xd7;<span class="number"><span class="float">20833.3</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.36</span></span>)</sub> + <sup><span class="number"><span class="float">3.736</span></span>&#xd7;<span class="number"><span class="float">-20833.3</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.4</span></span>)</sub> + <sup><span class="number"><span class="float">3.5429</span></span>&#xd7;<span class="number"><span class="float">20833.3</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.44</span></span>)</sub> + <sup><span class="number"><span class="float">3.3797</span></span>&#xd7;<span class="number"><span class="float">-20833.3</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.48</span></span>)</sub> + <sup><span class="number"><span class="float">3.2417</span></span>&#xd7;<span class="number"><span class="float">20182.3</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.52</span></span>)</sub> + <sup><span class="number"><span class="float">3.1209</span></span>&#xd7;<span class="number"><span class="float">-16666.7</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.56</span></span>)</sub> + <sup><span class="number"><span class="float">3.0138</span></span>&#xd7;<span class="number"><span class="float">8789.06</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.6</span></span>)</sub> + <sup><span class="number"><span class="float">2.8342</span></span>&#xd7;<span class="number"><span class="float">-3255.21</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.68</span></span>)</sub> + <sup><span class="number"><span class="float">2.6881</span></span>&#xd7;<span class="number"><span class="float">2669.27</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.76</span></span>)</sub> + <sup><span class="number"><span class="float">2.5662</span></span>&#xd7;<span class="number"><span class="float">-2604.17</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.84</span></span>)</sub> + <sup><span class="number"><span class="float">2.4242</span></span>&#xd7;<span class="number"><span class="float">2604.17</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.92</span></span>)</sub> + <sup><span class="number"><span class="float">2.3766</span></span>&#xd7;<span class="number"><span class="float">-2604.17</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1</span></span>)</sub> + <sup><span class="number"><span class="float">2.3058</span></span>&#xd7;<span class="number"><span class="float">2604.17</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.08</span></span>)</sub> + <sup><span class="number"><span class="float">2.2458</span></span>&#xd7;<span class="number"><span class="float">-2604.17</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.16</span></span>)</sub> + <sup><span class="number"><span class="float">2.2035</span></span>&#xd7;<span class="number"><span class="float">2604.17</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.24</span></span>)</sub> + <sup><span class="number"><span class="float">2.1661</span></span>&#xd7;<span class="number"><span class="float">-2522.79</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.32</span></span>)</sub> + <sup><span class="number"><span class="float">2.135</span></span>&#xd7;<span class="number"><span class="float">2083.33</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.4</span></span>)</sub> + <sup><span class="number"><span class="float">2.109</span></span>&#xd7;<span class="number"><span class="float">-1098.63</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.48</span></span>)</sub> + <sup><span class="number"><span class="float">2.0697</span></span>&#xd7;<span class="number"><span class="float">406.901</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.64</span></span>)</sub> + <sup><span class="number"><span class="float">2.0466</span></span>&#xd7;<span class="number"><span class="float">-333.659</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.8</span></span>)</sub> + <sup><span class="number"><span class="float">2.0325</span></span>&#xd7;<span class="number"><span class="float">325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.96</span></span>)</sub> + <sup><span class="number"><span class="float">2.0288</span></span>&#xd7;<span class="number"><span class="float">-325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.12</span></span>)</sub> + <sup><span class="number"><span class="float">2.0292</span></span>&#xd7;<span class="number"><span class="float">325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.28</span></span>)</sub> + <sup><span class="number"><span class="float">2.0228</span></span>&#xd7;<span class="number"><span class="float">-325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.44</span></span>)</sub> + <sup><span class="number"><span class="float">2.0124</span></span>&#xd7;<span class="number"><span class="float">325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.6</span></span>)</sub> + <sup><span class="number"><span class="float">2.0065</span></span>&#xd7;<span class="number"><span class="float">-325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.76</span></span>)</sub> + <sup><span class="number"><span class="float">2.0031</span></span>&#xd7;<span class="number"><span class="float">325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.92</span></span>)</sub> + <sup><span class="number"><span class="float">2.0015</span></span>&#xd7;<span class="number"><span class="float">-325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.08</span></span>)</sub> + <sup><span class="number"><span class="float">2.0008</span></span>&#xd7;<span class="number"><span class="float">325.521</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.24</span></span>)</sub> + <sup><span class="number"><span class="float">2.0004</span></span>&#xd7;<span class="number"><span class="float">-284.831</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.4</span></span>)</sub> + <sup><span class="number"><span class="float">2.0002</span></span>&#xd7;<span class="number"><span class="float">162.76</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.56</span></span>)</sub> + <sup><span class="number"><span class="float">2.0001</span></span>&#xd7;<span class="number"><span class="float">-40.6901</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.72</span></span>)</sub> &#x2227; Q(<I>x</I>) = <sup><span class="number"><span class="float">5.727</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.02</span></span>)</sub> + <sup><span class="number"><span class="float">5.544</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.04</span></span>)</sub> + <sup><span class="number"><span class="float">5.45</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.06</span></span>)</sub> + <sup><span class="number"><span class="float">5.351</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.08</span></span>)</sub> + <sup><span class="number"><span class="float">5.253</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.1</span></span>)</sub> + <sup><span class="number"><span class="float">5.157</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.12</span></span>)</sub> + <sup><span class="number"><span class="float">5.058</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.14</span></span>)</sub> + <sup><span class="number"><span class="float">4.96</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.16</span></span>)</sub> + <sup><span class="number"><span class="float">4.862</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.18</span></span>)</sub> + <sup><span class="number"><span class="float">4.762</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.2</span></span>)</sub> + <sup><span class="number"><span class="float">4.563</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.24</span></span>)</sub> + <sup><span class="number"><span class="float">4.36</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.28</span></span>)</sub> + <sup><span class="number"><span class="float">4.1584</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.32</span></span>)</sub> + <sup><span class="number"><span class="float">3.9463</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.36</span></span>)</sub> + <sup><span class="number"><span class="float">3.736</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.4</span></span>)</sub> + <sup><span class="number"><span class="float">3.5429</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.44</span></span>)</sub> + <sup><span class="number"><span class="float">3.3797</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.48</span></span>)</sub> + <sup><span class="number"><span class="float">3.2417</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.52</span></span>)</sub> + <sup><span class="number"><span class="float">3.1209</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.56</span></span>)</sub> + <sup><span class="number"><span class="float">3.0138</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.6</span></span>)</sub> + <sup><span class="number"><span class="float">2.8342</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.68</span></span>)</sub> + <sup><span class="number"><span class="float">2.6881</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.76</span></span>)</sub> + <sup><span class="number"><span class="float">2.5662</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.84</span></span>)</sub> + <sup><span class="number"><span class="float">2.4242</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">0.92</span></span>)</sub> + <sup><span class="number"><span class="float">2.3766</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1</span></span>)</sub> + <sup><span class="number"><span class="float">2.3058</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.08</span></span>)</sub> + <sup><span class="number"><span class="float">2.2458</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.16</span></span>)</sub> + <sup><span class="number"><span class="float">2.2035</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.24</span></span>)</sub> + <sup><span class="number"><span class="float">2.1661</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.32</span></span>)</sub> + <sup><span class="number"><span class="float">2.135</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.4</span></span>)</sub> + <sup><span class="number"><span class="float">2.109</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.48</span></span>)</sub> + <sup><span class="number"><span class="float">2.0697</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.64</span></span>)</sub> + <sup><span class="number"><span class="float">2.0466</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.8</span></span>)</sub> + <sup><span class="number"><span class="float">2.0325</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">1.96</span></span>)</sub> + <sup><span class="number"><span class="float">2.0288</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.12</span></span>)</sub> + <sup><span class="number"><span class="float">2.0292</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.28</span></span>)</sub> + <sup><span class="number"><span class="float">2.0228</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.44</span></span>)</sub> + <sup><span class="number"><span class="float">2.0124</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.6</span></span>)</sub> + <sup><span class="number"><span class="float">2.0065</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.76</span></span>)</sub> + <sup><span class="number"><span class="float">2.0031</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">2.92</span></span>)</sub> + <sup><span class="number"><span class="float">2.0015</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.08</span></span>)</sub> + <sup><span class="number"><span class="float">2.0008</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.24</span></span>)</sub> + <sup><span class="number"><span class="float">2.0004</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.4</span></span>)</sub> + <sup><span class="number"><span class="float">2.0002</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.56</span></span>)</sub> + <sup><span class="number"><span class="float">2.0001</span></span></sup>&#x2044;<sub>(<I>x</I> - <span class="number"><span class="float">3.72</span></span>)</sub>  
  
LaTex:  
  
  
[preview.pdf](https://github.com/boostorg/math/files/3908654/preview.pdf)

---

## Comment 33

> Username: NAThompson  
> Created_at: 2019-12-02 00:22:26 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560179969  

> Wild idea: are you using -ffast-math? I wonder if the compiler is optimising the call away (assuming it's seeing "x != x" or similar) and so isnan is always false?  
  
Yup that was it.  
  
The barycentric rational might be helped in presentation (for the example) by use of something shorter, but your idea of splitting up the numerator and denominator looks exactly right.

---

## Comment 34

> Username: jzmaddock  
> Created_at: 2019-12-02 08:33:07 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-560289174  

>Yup that was it.  
  
I thought this was a gcc bug, but a quick Google and apparently -ffast-math turns on -ffinite-math-only and removes all support for infinities and NaN's :(

---

## Comment 35

> Username: jzmaddock  
> Created_at: 2019-12-04 18:43:12 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-561783764  

@NAThompson : how does the data stored inside the Cardinal Trig interpolator relate to the coefficients of the sines and cosines?

---

## Comment 36

> Username: NAThompson  
> Created_at: 2019-12-04 19:34:29 UTC  
> Updated_at: 2019-12-04 21:45:41 UTC  
> Url: https://github.com/boostorg/math/pull/269#issuecomment-561804736  

@jzmaddock : Internally, it's just a polynomial. So you set z = e^{i\theta} to do the evaluation by Horner's method (see interpolators/detail/cardinal_trigonometric_detail.hpp:102) and you get  
  
q(t) = \gamma_0 +2Re{ \sum_{k = 1}^{N-1} \gamma_{k}z^{k}}  
  
That's the internal representation. Another, perhaps better representation for the user might be  
  
q(t) = \gamma_0 + 2Re{\sum_{k=1}^{N-1} \gamma_{k}\exp(2\pi i k (t-t_0)/T)}  
  
where \gamma_k, t_0, and T are numerical parameters passed by the user.  
  
Is it desirable to turn this into sines and cosines? Just gotta use de Moivre and break \gamma_k into real and imaginary parts.

---
