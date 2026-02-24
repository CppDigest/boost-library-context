# #87 - [Improvement] Try it online [Closed]

> Username: kris-jusiak  
> Created at: 2015-06-08 16:51:04 UTC  
> Updated at: 2015-06-12 22:50:20 UTC  
> Closed at: 2015-06-12 22:50:20 UTC  
> Url: https://github.com/boostorg/hana/issues/87  

I have recently added 'try it online badge' for proposed boost.di:  
http://krzysztof-jusiak.github.io/di/cpp14/boost/libs/di/doc/html/  
  
I think hana would benefit from this approach as well, especially just before the review.  
Possibly more people would be eager to test it and Wandbox is an awesome tool for it (it has clang-trunk and gcc-trunk, boost etc...)  
  
You can find the example with boost.di here:  
http://melpon.org/wandbox/permlink/O3ABWKU3VBV3WG8J

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-08 17:23:40 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110082697  

This is a **great** idea. I want this. How do you generate your `boost/di.hpp` header? Don't tell me you're copy/pasting everything by hand?

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-08 17:34:23 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110085400  

I just found it at [pp.sh](https://github.com/krzysztof-jusiak/di/blob/22d5c6ecb6048bc8e7b3d203fa2854a1c5fd9c36/tools/pph.sh), right? You're a serious bash master, you know that? I'll try to tweak it for my own use.

---

## Comment 3

> Username: kris-jusiak  
> Created at: 2015-06-09 10:22:56 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110306178  

Yep, that's the one ;) hehehe, the script was 5 line in the beginning, so couldn't be asked using anything more powerful, but oh well ;) anyway I adopted the script for hana and it actually works, so here you go  
  
``` sh  
main() {  
    generate_pph() {  
        echo $1 >> $2/tmp.hpp  
        cat $1 | egrep "^#include" | grep "boost\/hana" | while read include; do  
            file=`echo $include | sed "s/[^<^\"]*[\"<]\([^>\"]*\)[\">].*/\1/"`  
  
            if [[ "`cat $2/tmp.hpp | grep $file`" == "" ]]; then  
                generate_pph $file $2  
                cat $file | egrep "^#include" | grep -v "boost\/hana" >> $2/includes.hpp  
                echo >> $2/pph.hpp  
                cat $file | egrep -v "^#include" | cat -s >> $2/pph.hpp  
            fi  
        done  
    }  
  
    tmp_dir=`mktemp -d`  
  
    echo "#ifndef BOOST_HANA_PPH_HPP"  
    echo "#define BOOST_HANA_PPH_HPP"  
    generate_pph "boost/hana.hpp" "$tmp_dir"  
    cat $tmp_dir/includes.hpp | sort -u  
    cat $tmp_dir/pph.hpp  
    echo "#endif"  
  
    rm -rf $tmp_dir  
}  
  
cd "`readlink -f \`dirname $0\``/../include" && main "boost/hana.hpp" > "boost/hana_pph.hpp"  
```  
  
just run pph.sh and it will produce boost/hana_pph.hpp

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-09 16:20:49 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110420474  

Awesome, thanks! I tried to tweak it myself and it almost worked, but I had some weird problems. Anyway, the above works almost out-of-the box on OS X, except you must replace `mktemp -d` by `mktemp -d -t something`. So now I can generate the header, but I have a question for you. Ideally, I'd like to have a link in my README pointing to Wandbox, with this header included. However, I'd like this header to be generated automatically and the link to be updated in my README whenever I push to master. Are you handling this in DI? Otherwise, I'll try to set up something like that if I want to have this in the long term.

---

## Comment 5

> Username: ldionne  
> Created at: 2015-06-09 16:21:45 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110420948  

Because I'll inevitably forget to update the link at some point, and I don't want to have to think about it.

---

## Comment 6

> Username: kris-jusiak  
> Created at: 2015-06-09 16:34:20 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110424767  

Well, I haven't done it yet, but I would like to have exactly the same. Fortunately, Wandbox has a public API - https://github.com/melpon/wandbox/blob/master/kennel2/API.rst, which, I guess, might be used to achieve that, but yea, for now, I just copied the header to check it out. Will keep you informed tho.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-06-09 16:38:05 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110425907  

Same here, I'll let you know if I do it. Thanks a lot!

---

## Comment 8

> Username: kris-jusiak  
> Created at: 2015-06-09 17:02:07 UTC  
> Updated at: 2015-06-09 17:04:45 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110434324  

Actually, it seems to be very easy, basically works out of the box :)  
  
test.json  
  
```  
{  
  "codes": [  
      { "file": "di.hpp", "code": "#include <iostream>\nint main() { int x = 0; std::cout << \"hoge\" << std::endl; }" }  
  ],  
  "code" : "",  
  "options": "warning,gnu++1y",  
  "compiler": "gcc-head",  
  "compiler-option-raw": "-Dx=hogefuga\n-O3",  
  "save": true  
}  
```  
  
```  
curl -H "Content-type: application/json" -d "`cat test.json`"  http://melpon.org/wandbox/api/compile.json 2>&1 | grep url  
```  
  
gives:  
  
```  
"url" : "http://melpon.org/wandbox/permlink/yRx32XIzoUyZrMKm"  
```  
  
travis will handle that easily :)

---

## Comment 9

> Username: ldionne  
> Created at: 2015-06-09 17:05:53 UTC  
> Updated at: 2015-06-09 17:06:06 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110435300  

I was looking at the same thing :-). One problem I have is that the generated header for `boost/hana.hpp` seems to be too big; it does not work. I can't even share a link to it. (the header spans 33k lines lol)

---

## Comment 10

> Username: kris-jusiak  
> Created at: 2015-06-09 17:10:00 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110436203  

hmm, you may try to remove some doxygen comments or maybe with multiple "codes" in json. Although, the latter may not work, because wandbox seems not to be able to create directories :/

---

## Comment 11

> Username: ldionne  
> Created at: 2015-06-09 17:10:58 UTC  
> Url: https://github.com/boostorg/hana/issues/87#issuecomment-110436583  

Yeah, I'm witnessing precisely the same thing. Unfortunately, removing comments only trims the header by a couple of KLOCs, but I fear that's not enough.
