# #14 added Jamfile.v2 [Merged]

> Username: klemens-morgenstern  
> Created at: 2016-06-15 16:18:08 UTC  
> Updated at: 2016-06-16 19:03:51 UTC  
> Merged at: 2016-06-15 16:30:18 UTC  
> Closed at: 2016-06-15 16:30:18 UTC  
> Url: https://github.com/boostorg/histogram/pull/14  

Grüß Gott Hans,  
  
I added the bjam support for the library, though it does not include the python test (python_suite_test.py.in) and speed_vs_root.cpp.  
  
I hope that helps. If you want to build it, you just clone the histogram library into boost/libs and then the depencies should work out fine.   
  
LG,  
  
Klemens

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-06-15 16:21:32 UTC  
> Url: https://github.com/boostorg/histogram/pull/14#issuecomment-226240679  

[![Coverage Status](https://coveralls.io/builds/6610551/badge)](https://coveralls.io/builds/6610551)  
  
Coverage remained the same at 96.732% when pulling **b5fb18d42fbffa2e5ddd4a0102bb12343fbc4c1b on klemens-morgenstern:master** into **9fb7fb47def9923e705fa88f36693ed48a48600a on HDembinski:master**.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2016-06-15 16:28:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/14#issuecomment-226242632  

Lieber Klemens,  
  
super, vielen Dank!  
  
Ich habe gerade wenig Zeit, an Histogram weiterzufeilen, wollte aber   
noch die restlichen Issues bearbeiten und dann nochmal auf der   
Boost-Mailingliste nach einem Review fragen.  
  
Dank deines Jam-files ist ein weiterer Stolperstein ausgeraeumt!  
  
Beste Gruesse,  
  
Hans  
  
On 6/15/16 12:18 PM, klemens-morgenstern wrote:  
  
> Grüß Gott Hans,  
>   
> I added the bjam support for the library, though it does not include   
> the python test (python_suite_test.py.in) and speed_vs_root.cpp.  
>   
> I hope that helps. If you want to build it, you just clone the   
> histogram library into boost/libs and then the depencies should work   
> out fine.  
>   
> LG,  
>   
> Klemens  
>   
> ---  
>   
> ```  
>     You can view, comment on, or merge this pull request online at:  
> ```  
>   
> https://github.com/HDembinski/histogram/pull/14  
>   
> ```  
>     Commit Summary  
> ```  
> - added Jamfile.v2  
> - updated test  
>     
>   ```  
>   File Changes  
>   ```  
> - _A_ build/Jamfile.v2  
>   https://github.com/HDembinski/histogram/pull/14/files#diff-0 (24)  
> - _A_ test/Jamfile.v2  
>   https://github.com/HDembinski/histogram/pull/14/files#diff-1 (29)  
>     
>   ```  
>   Patch Links:  
>   ```  
> - https://github.com/HDembinski/histogram/pull/14.patch  
> - https://github.com/HDembinski/histogram/pull/14.diff  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/HDembinski/histogram/pull/14, or mute the thread   
> https://github.com/notifications/unsubscribe/ACgnohzliSqJAJglrpgLomKKY85D5Ylvks5qMCXAgaJpZM4I2iTi.  
  
##   
  
Hans Dembinski  
https://github.com/HDembinski  
  
Proposed for Boost: histogram  
https://github.com/HDembinski/histogram  
  
Automatic one-dimensional unfolding of data distributions  
https://github.com/HDembinski/aru-unfolding

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2016-06-16 06:48:13 UTC  
> Updated_at: 2016-06-16 06:48:30 UTC  
> Url: https://github.com/boostorg/histogram/pull/14#issuecomment-226401966  

Naja, das baut die doc noch nicht mit - insofern fehlt da noch was. Aber wenn ich das richtig verstehe hast du die html Doku schon fertig und das sphinx-Zeugs ist für das python-Modul? Dann wäre das wahrscheinlich in Ordnung so.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2016-06-16 18:36:55 UTC  
> Url: https://github.com/boostorg/histogram/pull/14#issuecomment-226574501  

Stimmt, den Sphinx-Kram braucht man nur, wenn man die Documentation neu bauen will. Entsprechend der Empfehlungen vom Boost Incubator liefere ich aber die aktuelle Docu in html stets mit. Vorausgesetzt das CMake-Zeug kann im Source bleiben (so habe ich die letzten Diskussionen auf der Mailingliste verstanden), dann ist das ausreichend.  
  
Was noch fehlt sind die Tests. Dein Jam-file baut keine der Tests, wenn ich richtig sehe. Ansonsten wirklich beeindruckend wie kurz das Jamfile ist, verglichen mit CMake, welches ja eher verbose ist.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2016-06-16 19:03:51 UTC  
> Url: https://github.com/boostorg/histogram/pull/14#issuecomment-226582209  

test/Jamfile.v2 baut die tests ;), d.h. die 4 C++-tests, ich wusste nicht wie das python Zeugs funktioniert.  
Du musst übrigens die Doku nicht fertig in der Repo mitliefern, es reicht, wenn in doc eine Jamfile liegt, die das ermöglicht, z.B. wie [hier](https://github.com/klemens-morgenstern/boost-process/tree/develop/doc). Da baut das mit quickbook und ich hab das gebaute auf meinem [github.io](http://klemens-morgenstern.github.io/process/) hochgeladen. Es gibt meines Wissens auch keine Regel in boost,build für Sphinx, wobei sich das sicher machen ließe.  
  
Wenn Du das ausprobieren willst, musst Du Deine Bibliothek nach boost/libs auschecken (auf windows muss der Ordner auch boost heißen). Dann kannst du in ./boost deine Bibo bauen:  
  
```  
b2 --with-histogram  
```  
  
Oder in boost/libs/histogram/test die tests mit `b2`ausführen. Vorher natürlich b2 bauen und in den PATH legen.

---
