# #64 - testing for equality fails for looooong numbers when checking at lower precision [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-13 19:16:34 UTC  
> Updated at: 2018-08-17 21:46:05 UTC  
> Closed at: 2018-08-17 21:46:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/64  

the following test fails for me:  
  
```  
BOOST_AUTO_TEST_CASE(precision_equality_default_differs)  
{  
	DefaultPrecision(1000);  
	mpfr_complex a("1.324719827394086120398419082734980126734089612309871092830981236748901273498071240986123094861246981263481263489016238947147129807419028748901273409127349087124612576129076541203975704195690418570914657910465091256016501650916509165097164509164509761409561097561097650791650971465097165097162059761209561029756019265019726509126509172650971625097162450971309756104975610274650917825018740981274098127409182375701465172340923847120836540491320467127043127893281461230951097260126309812374091265091824981231236409851274",  
		"-0.80743891267394610982659071452346156102764312401571972642394120395608291471029347812645125986123123904123471209381289471230512983491286102875870192091283712396550981723409812740981263471230498715096104897123094710923879065981740928740981271801391209238470129560941870129387409812883437894183883841283700483832883218128438938184289148239164079329657861209381892037483468937489237419236509823723705612893489712412306531274812364980127304981648712483248732");  
	DefaultPrecision(40);  
  
	BOOST_CHECK_EQUAL(a,a);  
}  
```  
  
does not fail if the change to precision is not made.  `DefaultPrecision` changes precision for both reals and complexes.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-15 18:03:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/64#issuecomment-413283416  

Works for me.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-08-16 14:57:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/64#issuecomment-413575292  

hmm.  i just pulled and the test is still failing for me.    
  
i am on osx,   
```  
Apple LLVM version 9.0.0 (clang-900.0.39.2)  
Target: x86_64-apple-darwin16.7.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
  
any advice?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-08-16 17:22:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/64#issuecomment-413621483  

Ah, never mind, I was only changing precision on one type not both.  Reproduced.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-08-17 08:11:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/64#issuecomment-413791678  

Can you please try again, should be fixed now.

---

## Comment 5

> Username: ofloveandhate  
> Created at: 2018-08-17 21:46:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/64#issuecomment-413996236  

fixed!  🎊
