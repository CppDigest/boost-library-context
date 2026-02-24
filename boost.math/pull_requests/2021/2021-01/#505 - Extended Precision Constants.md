# #505 Extended Precision Constants [Closed]

> Username: mborland  
> Created at: 2021-01-30 11:04:59 UTC  
> Updated at: 2024-02-22 10:26:40 UTC  
> Closed at: 2024-02-22 10:26:40 UTC  
> Url: https://github.com/boostorg/math/pull/505  

As requested by @ckormanyos in the feature wish list. Does this interface look like what you had envisioned? Currently has a trivial (long double) version of pi and then the first 10'000 digits for non-trivial types.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-01-30 11:34:29 UTC  
> Updated_at: 2021-01-30 11:36:42 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770198467  

This is cool. I never really thought of that expressional form. Nice!  
  
If I catch your intention, you are picking up a bit on the syntax of C++20's `<numbers>` library, right?  
  
I actually like to use an older form of syntax for the character strings. You can decide on column widths of 1,000 decimal digits or 100 decimal digits. An example with column widths of 1,000 decimal digist would look something like this below.  
  
Typing is tedious for sure. But I like columns of 100 decimal digits because of the order it adds. We tend to think in groups of 10^n.  
  
```  
template<typename T>  
const T pi = T(  
"3"  
"1415926535897932384626433832795028841971693993751058209749445923078164062862089986280348253421170679821480865132823066470938446095505822317253594081284811174502841027019385211055596446229489549303819644288109756659334461284756482337867831652712019091456485669234603486104543266482133936072602491412737245870066063155881748815209209628292540917153643678925903600113305305488204665213841469519415116094330572703657595919530921861173819326117931051185480744623799627495673518857527248912279381830119491298336733624406566430860213949463952247371907021798609437027705392171762931767523846748184676694051320005681271452635608277857713427577896091736371787214684409012249534301465495853710507922796892589235420199561121290219608640344181598136297747713099605187072113499999983729780499510597317328160963185950244594553469083026425223082533446850352619311881710100031378387528865875332083814206171776691473035982534904287554687311595628638823537875937519577818577805321712268066130019278766111959092164201989"  
"3809525720106548586327886593615338182796823030195203530185296899577362259941389124972177528347913151557485724245415069595082953311686172785588907509838175463746493931925506040092770167113900984882401285836160356370766010471018194295559619894676783744944825537977472684710404753464620804668425906949129331367702898915210475216205696602405803815019351125338243003558764024749647326391419927260426992279678235478163600934172164121992458631503028618297455570674983850549458858692699569092721079750930295532116534498720275596023648066549911988183479775356636980742654252786255181841757467289097777279380008164706001614524919217321721477235014144197356854816136115735255213347574184946843852332390739414333454776241686251898356948556209921922218427255025425688767179049460165346680498862723279178608578438382796797668145410095388378636095068006422512520511739298489608412848862694560424196528502221066118630674427862203919494504712371378696095636437191728746776465757396241389086583264599581339047802759009"  
"9465764078951269468398352595709825822620522489407726719478268482601476990902640136394437455305068203496252451749399651431429809190659250937221696461515709858387410597885959772975498930161753928468138268683868942774155991855925245953959431049972524680845987273644695848653836736222626099124608051243884390451244136549762780797715691435997700129616089441694868555848406353422072225828488648158456028506016842739452267467678895252138522549954666727823986456596116354886230577456498035593634568174324112515076069479451096596094025228879710893145669136867228748940560101503308617928680920874760917824938589009714909675985261365549781893129784821682998948722658804857564014270477555132379641451523746234364542858444795265867821051141354735739523113427166102135969536231442952484937187110145765403590279934403742007310578539062198387447808478489683321445713868751943506430218453191048481005370614680674919278191197939952061419663428754440643745123718192179998391015919561814675142691239748940907186494231961"  
"5679452080951465502252316038819301420937621378559566389377870830390697920773467221825625996615014215030680384477345492026054146659252014974428507325186660021324340881907104863317346496514539057962685610055081066587969981635747363840525714591028970641401109712062804390397595156771577004203378699360072305587631763594218731251471205329281918261861258673215791984148488291644706095752706957220917567116722910981690915280173506712748583222871835209353965725121083579151369882091444210067510334671103141267111369908658516398315019701651511685171437657618351556508849099898599823873455283316355076479185358932261854896321329330898570642046752590709154814165498594616371802709819943099244889575712828905923233260972997120844335732654893823911932597463667305836041428138830320382490375898524374417029132765618093773444030707469211201913020330380197621101100449293215160842444859637669838952286847831235526582131449576857262433441893039686426243410773226978028073189154411010446823252716201052652272111660396"  
"6655730925471105578537634668206531098965269186205647693125705863566201855810072936065987648611791045334885034611365768675324944166803962657978771855608455296541266540853061434443185867697514566140680070023787765913440171274947042056223053899456131407112700040785473326993908145466464588079727082668306343285878569830523580893306575740679545716377525420211495576158140025012622859413021647155097925923099079654737612551765675135751782966645477917450112996148903046399471329621073404375189573596145890193897131117904297828564750320319869151402870808599048010941214722131794764777262241425485454033215718530614228813758504306332175182979866223717215916077166925474873898665494945011465406284336639379003976926567214638530673609657120918076383271664162748888007869256029022847210403172118608204190004229661711963779213375751149595015660496318629472654736425230817703675159067350235072835405670403867435136222247715891504953098444893330963408780769325993978054193414473774418426312986080998886874132604721"  
"5695162396586457302163159819319516735381297416772947867242292465436680098067692823828068996400482435403701416314965897940924323789690706977942236250822168895738379862300159377647165122893578601588161755782973523344604281512627203734314653197777416031990665541876397929334419521541341899485444734567383162499341913181480927777103863877343177207545654532207770921201905166096280490926360197598828161332316663652861932668633606273567630354477628035045077723554710585954870279081435624014517180624643626794561275318134078330336254232783944975382437205835311477119926063813346776879695970309833913077109870408591337464144282277263465947047458784778720192771528073176790770715721344473060570073349243693113835049316312840425121925651798069411352801314701304781643788518529092854520116583934196562134914341595625865865570552690496520985803385072242648293972858478316305777756068887644624824685792603953527734803048029005876075825104747091643961362676044925627420420832085661190625454337213153595845068772460"  
"2901618766795240616342522577195429162991930645537799140373404328752628889639958794757291746426357455254079091451357111369410911939325191076020825202618798531887705842972591677813149699009019211697173727847684726860849003377024242916513005005168323364350389517029893922334517220138128069650117844087451960121228599371623130171144484640903890644954440061986907548516026327505298349187407866808818338510228334508504860825039302133219715518430635455007668282949304137765527939751754613953984683393638304746119966538581538420568533862186725233402830871123282789212507712629463229563989898935821167456270102183564622013496715188190973038119800497340723961036854066431939509790190699639552453005450580685501956730229219139339185680344903982059551002263535361920419947455385938102343955449597783779023742161727111723643435439478221818528624085140066604433258885698670543154706965747458550332323342107301545940516553790686627333799585115625784322988273723198987571415957811196358330059408730681216028764962867"  
"4460477464915995054973742562690104903778198683593814657412680492564879855614537234786733039046883834363465537949864192705638729317487233208376011230299113679386270894387993620162951541337142489283072201269014754668476535761647737946752004907571555278196536213239264061601363581559074220202031872776052772190055614842555187925303435139844253223415762336106425063904975008656271095359194658975141310348227693062474353632569160781547818115284366795706110861533150445212747392454494542368288606134084148637767009612071512491404302725386076482363414334623518975766452164137679690314950191085759844239198629164219399490723623464684411739403265918404437805133389452574239950829659122850855582157250310712570126683024029295252201187267675622041542051618416348475651699981161410100299607838690929160302884002691041407928862150784245167090870006992821206604183718065355672525325675328612910424877618258297651579598470356222629348600341587229805349896502262917487882027342092222453398562647669149055628425039127"  
"5771028402799806636582548892648802545661017296702664076559042909945681506526530537182941270336931378517860904070866711496558343434769338578171138645587367812301458768712660348913909562009939361031029161615288138437909904231747336394804575931493140529763475748119356709110137751721008031559024853090669203767192203322909433467685142214477379393751703443661991040337511173547191855046449026365512816228824462575916333039107225383742182140883508657391771509682887478265699599574490661758344137522397096834080053559849175417381883999446974867626551658276584835884531427756879002909517028352971634456212964043523117600665101241200659755851276178583829204197484423608007193045761893234922927965019875187212726750798125547095890455635792122103334669749923563025494780249011419521238281530911407907386025152274299581807247162591668545133312394804947079119153267343028244186041426363954800044800267049624820179289647669758318327131425170296923488962766844032326092752496035799646925650493681836090032380929345"  
"9588970695365349406034021665443755890045632882250545255640564482465151875471196218443965825337543885690941130315095261793780029741207665147939425902989695946995565761218656196733786236256125216320862869222103274889218654364802296780705765615144632046927906821207388377814233562823608963208068222468012248261177185896381409183903673672220888321513755600372798394004152970028783076670944474560134556417254370906979396122571429894671543578468788614445812314593571984922528471605049221242470141214780573455105008019086996033027634787081081754501193071412233908663938339529425786905076431006383519834389341596131854347546495569781038293097164651438407007073604112373599843452251610507027056235266012764848308407611830130527932054274628654036036745328651057065874882256981579367897669742205750596834408697350201410206723585020072452256326513410559240190274216248439140359989535394590944070469120914093870012645600162374288021092764579310657922955249887275846101264836999892256959688159205600101655256375678"  
"56672");  
```

---

## Comment 2

> Username: mborland  
> Created_at: 2021-01-30 11:48:43 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770200103  

The syntax is very similar to `<numbers>`; ideally you could replace `std::` with `boost::math::` to extend precision. Changing column width would make sense. Habitually I use in the range of 120 but I agree with you about grouping by 10^n.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-01-30 12:45:09 UTC  
> Updated_at: 2021-01-30 12:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770207088  

> The syntax is very similar to `<numbers>`; ideally you could replace `std::` with `boost::math::` to extend precision.  
  
Awesome! Or use `using` I imagine as well?  
  
> Changing column width would make sense. Habitually I use in the range of 120 but I agree with you about grouping by 10^n.  
  
Thank you.  
...Or if like me, so old you still want to restrict to column width 80...

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-01-30 13:33:50 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770213409  

Before I comment in detail.... how does this differ from boost::math::constants ?  
  
OK I realise the syntax can be cleaner now that we're not C++03, but you could equally have written  
  
```  
template<typename T>  
inline constexpr T pi_v() noexcept(some-condition)  
{  
  return boost::math::constants::pi<T>();  
}  
```  
  
And yes, the constants:: versions are constexpr and arbitrary precision already ;)

---

## Comment 5

> Username: mborland  
> Created_at: 2021-01-30 14:00:59 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770216830  

Syntax aside I think the only major difference is the level of precision afforded to constexpr, or without calculation. Correct me if I am wrong but `boost::math::constants` looks to generally deliver 117 digits before `compute` is called at runtime. This will give you the first 10'000.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2021-01-30 14:34:32 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770221266  

So for a lot of calculations-pi is certainly one of them, e another, computing the number is actually faster than pulling it off disk. That's a danger with this sort of thing. In addition, the parse is gonna be expensive.  
  
I'm intrigued by this attack but it's unclear to me that it's an improvement.  
  
It reminds me of the compression competitions where they had to make a rule that the winner must minimize the size of the data + compressor. We need to think about ways this might be *unexpectedly* expensive.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-01-30 14:55:52 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770224081  

> We need to think about ways this might be _unexpectedly_ expensive.  
  
Good advice to ponder! Or maybe the big strings even bring in unexpected dangers to break existing code or compiler memory systems somewhere out there if we try to parse 20 constants having 10,000 digits each.  
  
When I did some early, early research on this issue, as a compromise, I limited the string-constructables to a handful of constants, 1,101 digits.  
  
Maybe the 10,000 is a bit to rowdy?  
  
Thoughts?

---

## Comment 8

> Username: mborland  
> Created_at: 2021-01-30 15:13:01 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770226399  

At runtime @NAThompson's suspicions are correct:  
```  
-----------------------------------------------------  
Benchmark           Time             CPU   Iterations  
-----------------------------------------------------  
Pi/128          0.038 us        0.038 us     18573162  
Pi/256          0.039 us        0.039 us     17684845  
Pi/512          0.041 us        0.041 us     17272975  
Pi/1024         0.044 us        0.044 us     15709507  
Pi/2048         0.051 us        0.050 us     15178055  
Pi/4096         0.081 us        0.081 us      8417465  
Pi/8192         0.107 us        0.107 us      6424523  
Pi_BigO          5.92 lgN        5.91 lgN    
Pi_RMS             27 %            27 %      
Pi_v/128         12.3 us         12.3 us        57430  
Pi_v/256         12.8 us         12.8 us        53484  
Pi_v/512         13.5 us         13.4 us        50292  
Pi_v/1024        16.5 us         16.5 us        42674  
Pi_v/2048        25.6 us         25.6 us        27304  
Pi_v/4096        54.8 us         54.7 us        12592  
Pi_v/8192         140 us          139 us         5004  
Pi_v_BigO        1.28 NlgN       1.28 NlgN   
Pi_v_RMS           19 %            19 %    
```  
  
My question than becomes is there a use case for having >117 of digits of precision at compile time? That would really be the differentiating factor.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2021-01-30 16:49:34 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770242503  

>Syntax aside I think the only major difference is the level of precision afforded to constexpr, or without calculation. Correct me if I am wrong but boost::math::constants looks to generally deliver 117 digits before compute is called at runtime. This will give you the first 10'000.  
  
It has layers of precision:  
  
* Up to quad precision is constexpr and returns a literal.  
* Up to 200 decimal places is computed from a string - provided T is constructible from a `const char*`  
* After that, runtime computation is used.  
  
>So for a lot of calculations-pi is certainly one of them, e another, computing the number is actually faster than pulling it off disk. That's a danger with this sort of thing. In addition, the parse is gonna be expensive.  
  
I think it depends on the constant as well.  We actually have a number of competing constraints here:  
  
* Compile time cost:  really large files (big strings) are an issue here.  
* Object file size: If we have large strings encoded in the file, even though we may only be using a few of those digits then this may be an issue for some users.  
* Runtime cost: this depends on the constant, some are cheaper than others to compute on the fly, some are really expensive (Catalan from memory, but I could be wrong about that).  
* Runtime precision: generally conversion from string should yield a nearest-bit result, where as computation on the fly is likely to be a few eps out.  
* constexpr: currently we have no types larger than `__float128` which are constexpr-constructible.  I've enabled constexpr arithmetic for cpp_int, so cpp_bin_float is within reach.  It is potentially horribly expensive in compile times though :(  Another thing we could explore are user-defined literals for cpp_bin_float, maybe floating point values in general.  It's bloody horrible programming though - I did this for cpp_int literals but didn't enjoy it much!  Other types like mpfr and gmp are almost certainly forever out of reach as far as constexpr goes.  
* constexpr is not a magic bullet: remember that constexpr functions will still be runtime evaluated, _unless_ they are used in a constexpr context.  To fix that we would need consteval which is C++20 I think.  
  
Having said all that, this might be the time to rethink the interface to the Math lib constants now that we're not constrained by C++03.  Coming up with a good way to manage the transition would be the biggest challenge I suspect?

---

## Comment 10

> Username: NAThompson  
> Created_at: 2021-01-30 16:58:24 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770243781  

@mborland : I believe that what @ckormanyos was talking about in the feature wishlist was to make sure that the algorithms used to compute the constants were state of the art, so that the complexity of recovering each bit was as low as possible. 10,000 digits is just a stretch goal for that enterprise. (Is that correct Chris?)

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2021-01-30 17:38:43 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770249187  

>  talking about in the feature wishlist was to make sure that the algorithms used to compute the constants were state of the art, so that the complexity of recovering each bit was as low as possible. 10,000 digits is just a stretch goal for that enterprise. (Is that correct Chris?)  
  
In a word, yes.  
  
In #303, I wrote:  
- Extend constants to higher precision such as 10,000 decimal digits and get them tested.  
  
The context of that wishlist entry was expressing an intent to improve the performance/efficiency of our constants calculations, with particular focus on those calculated via AGM algorithms, such as pi-related, root-related and logarithm-related constants. At that time, we were putting the finishing touches on Karatsuba multiplication for `cpp_bin_float`. Having finished that, the goal of efficiency has been reached for pi-related constants with `cpp_bin_float`. I do, in fact, intend to contribute Karatsuba-based multiplication for `cpp_dec_float` (in my target) this year, thus bringing decimal float to the same general state as binary float in Multiprecision.  
  
Returning to the root-based and logarithm-based constants, I still need to incormporate some of the key elements of the work from GSoC 2020 to improve the efficiency of high-precision logarithm-based constants, above a few hundred decimal digits. In my opinion, these are definitely worth the expected few tens of lines needed for the performance kicks. Also root-based constants can be significantly improved with the same general scope of effort.  
  
Some constants such as Catalan's constant will always remain difficult to compute, even with accelrated series methods. Admitedly, however, we do not perceive a loud cry in our community for tens of thousands of digits of Catalan's constant or other really special ones.  
  
So having said all that, I am particularly motivated by the advices of Nick and John, emphasizing caution with regard to incorporation of very large string-based compilation units.  
  
We could re-asses the meaning of providing about 117 decimal digits in string form. Does that have a physical derivation from somewhere? Is it just a nice amount that fits rather well on a modern page of code text? Or would we like to extend to a different number (albeit maybe not so stellarly high as 10,000) such as 129 or 201?

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2021-01-30 17:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770250754  

>We could re-asses the meaning of providing about 117 decimal digits in string form. Does that have a physical derivation from somewhere? Is it just a nice amount that fits rather well on a modern page of code text? Or would we like to extend to a different number (albeit maybe not so stellarly high as 10,000) such as 129 or 201?  
  
Current string form constants provide 200-decimal place precision, it's an arbitrary number!

---

## Comment 13

> Username: mborland  
> Created_at: 2021-01-30 18:23:48 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770256256  

> Having said all that, this might be the time to rethink the interface to the Math lib constants now that we're not constrained by C++03. Coming up with a good way to manage the transition would be the biggest challenge I suspect?  
  
Do you have anything particular in mind, or is it more of refactoring to C++11? `<numbers>` syntax allows `pi<double>` vice `pi<double>()` but this is dependent on variable templates (C++14). Even reducing the string size to 1101 doesn't do much to help performance compared to the current implementation. Would it be worth incorporating a separate interface (like high_precision_constants here) with a caveat to the user that it is likely higher precision but it's not a free lunch?

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2021-01-30 18:51:34 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770263119  

>Do you have anything particular in mind, or is it more of refactoring to C++11? <numbers> syntax allows pi<double> vice pi<double>() but this is dependent on variable templates (C++14). Even reducing the string size to 1101 doesn't do much to help performance compared to the current implementation. Would it be worth incorporating a separate interface (like high_precision_constants here) with a caveat to the user that it is likely higher precision but it's not a free lunch?  
  
Yes I was thinking of `pi<T>` rather than `pi<T>()`, but if it's C++14 we can't default to that just yet :(  
  
We could as you say have a high precision interface with the large strings in place, I guess the question is how much it will be used?  @ckormanyos what was your use case for very high digit counts?

---

## Comment 15

> Username: NAThompson  
> Created_at: 2021-01-30 18:55:32 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770263741  

>  @ckormanyos what was your use case for very high digit counts?  
  
Not Chris but mine was for [PSLQ](https://github.com/boostorg/multiprecision/pull/259). The syntax of the constants library was really no problem for me; it's more the algorithms to generate those high digit counts being slow-although admittedly I don't know that faster algorithms exist.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2021-01-30 19:14:39 UTC  
> Updated_at: 2021-01-30 19:17:07 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770266378  

> what was your use case for very high digit counts?  
  
The tried and true... because they're there... comes to mind.  
  
I hammer out a million or a billion digits of pi as a matter of principle to test new computer/compiler systems --- also emedded controllers, their compilers and memory systems, usually with the modest million digits.  
  
I know if the C++11 compiler, the containers, custom allocators, the processor's FPU, memory system, caching, etc. handle the million digits error free, then things are looking good for middle-of-the-road embedded work in modern C++ on the system.  
  
I'll go up to 1,000 decimal digits or slightly higher for fractal imaging, but personally don't go deeper diving than that. Over the years, I did also identify high-precision numerical calculations in template-based modern C++ to be a really deep stress test of a real-time embedded compiler's C++ abilities --- that was a tangential discovery that influences some of the research work I am doing to this very day.

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2021-01-30 19:35:26 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770269034  

> hammer out a million or a billion digits of pi as a matter of principle to test new computer/compiler systems  
  
Allow me to expand on that. I am a huge fan of Boost.Math and Boost.Multiprecision because of the modern template scalable treatment of numbers, constants and the special functions of pure and applied mathematics (and a whole lot more). I still have certain goals. In the future, I would like to be able to pull a million digits of pi or ln_two off the header-only rack of Boost any time, anywhere, portable, with any new compiler and no external libs whatsoever. (This I do crave, and it comes in addition to the countless built-in functions and lower-precision functions in multiprecision/math.)  
  
We are not quite there yet, but really getting rather close.

---

## Comment 18

> Username: mborland  
> Created_at: 2021-01-30 19:54:39 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770271687  

@jzmaddock 27fc8b6 offers an example of a variable template wrapper around the current constant implementations. It matches the syntax from C++20s `<numbers>` in that you would call `boost::math::numbers::pi_v<double>`. It only works for trivial types because variable templates do not allow much in the way of SFINAE (constexpr fails with multiprecision types even when not evaluated as constexpr).

---

## Comment 19

> Username: pabristow  
> Created_at: 2021-01-31 10:45:54 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770361969  

Don’t forget that std::constants was approved for C++20  see https://github.com/cplusplus/papers/issues/107  
  
https://wiki.edg.com/pub/Wg21cologne2019/LibraryWorkingGroup/D0631R8.pdf   (reminder template)  
  
This is pretty much the cmath list at 128-bit precision (35ish decimal digits - increased from 80-bit at my suggestion).  
  
And this will meet most peoples needs?  
  
But looks a bit different from Boost.Math constants (and is a much smaller subset of values).  
  
I would have thought that it was only worth making any big changes to Boost.Math (to provide many more values not in the std::constants proposal) if we can make it work with a similar syntax to std::constants (meaning c++>11 and probably 20?), and avoid a compiler-time penalty for getting simple double pi.  John's cunning scheme achieves a balance of a small penalty for double yet still providing a lot more digits for the greedy who want more and more pi!

---

## Comment 20

> Username: mborland  
> Created_at: 2021-01-31 12:23:05 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770374048  

@pabristow https://github.com/boostorg/math/commit/27fc8b6600fefd61c3709d4b42f773f53bc1c7b5 offers an example of a thin wrapper around the current implementation to give the same syntax as `<numbers>`:  
  
```  
template<typename T, typename std::enable_if<std::is_trivially_constructible<T>::value, bool>::type = true>  
BOOST_INLINE_VARIABLE constexpr T pi_v = boost::math::constants::pi<T>();  
  
BOOST_INLINE_VARIABLE constexpr double pi = pi_v<double>;  
```  
  
This allows `boost::math::numbers::pi_v<double>` to be called in lieu of `std::numbers::pi_v<double>`. Like you said wrapping everything available would give a much more comprehensive set of constants than the standard library offers. Is it worth pursuing wrapping constants to match the syntax of the standard library?

---

## Comment 21

> Username: pabristow  
> Created_at: 2021-01-31 12:55:47 UTC  
> Url: https://github.com/boostorg/math/pull/505#issuecomment-770378382  

Yes - but premature until many compilers support c++20 and <numbers>?  
  
On MSVC 14.2 latest preview, <numbers> exists, but is not usable by the MS VS compiler, only Clang, for example.

---

## Review 22 [Commented]

> Username: kcbsbo  
> Created_at: 2021-08-31 16:16:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/505#pullrequestreview-742938721  

📁 test/test_high_precision_constants.cpp

```diff
  23 |+     T new_pi = boost::math::numbers::pi_hp<T>();
  24 |+ 
  25 |+     BOOST_TEST(abs(old_pi-new_pi) < tol);
```

> Username: kcbsbo  
> Created_at: 2021-08-31 16:16:10 UTC  
> Updated_at: 2021-08-31 16:16:11 UTC  
> Url: https://github.com/boostorg/math/pull/505#discussion_r699480162  

i do not think it's correct to compare float in this way


---
