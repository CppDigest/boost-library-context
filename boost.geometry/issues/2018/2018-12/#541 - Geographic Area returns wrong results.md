# #541 - Geographic Area returns wrong results [Open]

> Username: barendgehrels  
> Created at: 2018-12-16 17:21:41 UTC  
> Updated at: 2019-01-23 15:05:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/541  

Hi @vissarion , @awulkiew ,  
I'm busy producing buffers in geographical coordinate system around a point. That is work in progress now, and it starts to work. However, when I want to check the area (of the geographic buffer) I get unexpected results.  
  
Below the debug output, containing:   
number of input points, "geographic", number of output points (one more), area-in-geographic, "projected", number of output points (still one more as input), area-in-cartesian. Below that their WKT representations in both geographic and projected (using sterea for the Dutch coordinate system, but that is not relevant for this issue)  
  
Because I specified one meter, one would expect an area of pi. Because the number of points is low, it is less, but in cartesian (I used Boost.Geometry projection/transformation), that all seems allright.  
  
So I have to conclude that the area calculated by boost::geometry::area for a geographic coordinate system is not right... Or maybe I had to specify another strategy, but I debugged the code, and basically it seems to take the right strategies.  
  
You see the geographic area fluctiating from -7106 via --51940 to +13597 etc. In only one case (not below, because from a previous debugging version) I got a value close to a right value...  
  
```  
5  geographic  6   -7106.98  projected  6   2.3767  
     MULTIPOLYGON(((4.900000014692011 52.40000898675671,4.900013977465128 52.40000276851281,4.900008623854553 52.39999272427848,4.899991352373283 52.39999273484305,4.899986031615026 52.40000278560664,4.900000014692011 52.40000898675671)))  
     MULTIPOLYGON(((121816.3935228445 490239.7834838452,121817.3390161667 490239.0853036283,121816.9671865939 490237.9703284562,121815.7918899588 490237.9794161207,121815.437346262 490239.1000077746,121816.3935228445 490239.7834838452)))  
7  geographic  8   -51940.44953270301  projected  8   2.735323085114942  
     MULTIPOLYGON(((4.900000014692011 52.40000898675671,4.900011495831415 52.40000559612448,4.90001432037284 52.39999799149619,4.900006361382522 52.39999189931258,4.899993612143401 52.39999190711099,4.899985673088598 52.39999800901907,4.899988522489213 52.40000561017674,4.900000014692011 52.40000898675671)))  
     MULTIPOLYGON(((121816.3935228445 490239.7834838452,121817.1722615819 490239.40101414,121817.3587729585 490238.5537002923,121816.8126101027 490237.8795869154,121815.945044786 490237.8862951292,121815.4093713848 490238.5687735133,121815.6089628978 490239.4131019245,121816.3935228445 490239.7834838452)))  
9  geographic  10   -28607.43220085142  projected  10   2.891395106998971  
     MULTIPOLYGON(((4.900000014692011 52.40000898675671,4.900009455092996 52.4000068784781,4.900014471349158 52.4000015516828,4.900012716299604 52.39999549883819,4.900005011153675 52.39999155213724,4.89999496123439 52.39999155828457,4.899987269008388 52.39999551440371,4.899985533753316 52.40000156938328,4.899990567416462 52.40000689003125,4.900000014692011 52.40000898675671)))  
     MULTIPOLYGON(((121816.3935228445 490239.7834838452,121817.0343521173 490239.5446116999,121817.3717130748 490238.9497042377,121817.2477507743 490238.2771252795,121816.720468555 490237.841581988,121816.0365876275 490237.8468699147,121815.5161034769 490238.2905147722,121815.4025564223 490238.9649302114,121815.7490763939 490239.554549747,121816.3935228445 490239.7834838452)))  
11  geographic  12   144878.6498449351  projected  12   2.97234318849587  
     MULTIPOLYGON(((4.900000014692011 52.40000898675671,4.90000795545699 52.40000755528196,4.900013370419436 52.40000372505826,4.900014540367223 52.39999871215498,4.900011093851504 52.39999410813363,4.900004125117584 52.39999137473812,4.899995846688667 52.39999137980185,4.89998888690606 52.3999941217171,4.899985455450992 52.39999872994556,4.899986641787119 52.40000374140755,4.899992069262416 52.40000756499918,4.900000014692011 52.40000898675671)))  
     MULTIPOLYGON(((121816.3935228445 490239.7834838452,121816.9328105242 490239.6205935691,121817.2984238039 490239.1919980304,121817.3742830501 490238.6337732781,121817.1363034876 490238.1231517321,121816.6600419474 490237.8222521214,121816.0967081027 490237.8266079476,121815.6251564683 490238.1348362649,121815.3951013562 490238.6490767669,121815.4795836401 490239.2060617174,121815.8517807918 490239.6289523419,121816.3935228445 490239.7834838452)))  
13  geographic  14   13597.64092986423  projected  14   3.019500571803292  
     MULTIPOLYGON(((4.900000014692011 52.40000898675671,4.900006840724092 52.40000795320133,4.900012099627947 52.4000050976631,4.900014586651976 52.40000107431168,4.900013732049592 52.39999680484858,4.900009731600762 52.39999326735627,4.900003501760424 52.39999127223135,4.899996469709414 52.3999912765327,4.899990246404989 52.39999327927492,4.899986257530689 52.39999682165411,4.899985416889869 52.40000109215416,4.89998791706407 52.40000511245501,4.899993185294162 52.40000796155404,4.900000014692011 52.40000898675671)))  
     MULTIPOLYGON(((121816.3935228445 490239.7834838452,121816.8572522716 490239.6653730333,121817.212975916 490239.3452833782,121817.3792017778 490238.8965435647,121817.3178495146 490238.4219544754,121817.042974191 490238.0302387534,121816.6175464315 490237.8111337412,121816.1390266085 490237.8148337687,121815.7170378461 490238.0404911955,121815.4482526841 490238.4364106264,121815.3942465646 490238.911891695,121815.5673916392 490239.3580074163,121815.9280224574 490239.6725580526,121816.3935228445 490239.7834838452)))  
15  geographic  16   34802.96352132662  projected  16   3.049312928096697  
     MULTIPOLYGON(((4.900000014692011 52.40000898675671,4.900005989198468 52.40000820615538,4.900010928117561 52.40000600663501,4.900013977465128 52.40000276851281,4.900014609981563 52.3999990516897,4.900012716299604 52.39999549883819,4.900008623854553 52.39999272427848,4.900003040267405 52.399991207757,4.899996930990699 52.39999121149386,4.899991352373283 52.39999273484303,4.899987269008388 52.39999551440371,4.899985386946967 52.39999906956475,4.899986031615026 52.40000278560664,4.899989091544179 52.40000601999194,4.899994037645167 52.40000821346587,4.900000014692011 52.40000898675671)))  
     MULTIPOLYGON(((121816.3935228445 490239.7834838452,121816.7994963964 490239.693904431,121817.133936763 490239.4469437796,121817.3390161667 490239.0853036283,121817.3792745153 490238.6715148503,121817.2477507743 490238.2771252795,121816.9671865939 490237.9703284562,121816.5860940701 490237.8041723452,121816.1703675617 490237.80738683,121815.7918899588 490237.9794161183,121815.5161034769 490238.2905147722,121815.3906941045 490238.6868910086,121815.437346262 490239.1000077746,121815.6479933675 490239.4584334347,121815.9862126222 490239.7001929283,121816.3935228445 490239.7834838452)))  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2018-12-17 10:44:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-447800791  

Hi @barendgehrels  I looked at the issue. It seems that the problem does not come from the area algorithm but from the initialization of the geometry. How are you initializing your geometries?   
  
For your first example polygon in your list, I tried the following cases of coordinate types (used to parameterize everything, i.e. points, strategies, spheroid) and initialization methods:  
  
|  coordinate type        | initialization method           | area result  |  
| ------------- |:-------------:| -----:|  
| `double`    | `read_wkt` | -871.955 |  
| `double`    | `c++11 brace initialization` | -871.955 |  
| `long double`    | `read_wkt` | 2.06332 |  
| `long double`    | `c++11 brace initialization` | 2.20349 |  
  
In all cases, the resulting polygon was different and thus the area. Note that since the input coordinates are in degrees and the result in m^2 a small perturbation in the input (that may come from accuracy loss at initialization) will result a large difference in the output. This explains the large variance of your results.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2018-12-17 19:49:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-447975113  

Hi @vissarion , these geometries are calculated by my concept buffer-around-point-in-geo implementation. Using the direct formula as used in the other ticket. So basically I think the initialization is perfect, above all because if I project it (as I did) and then calculate the area, I get the correct results. So the geometry itself should be fine...  
  
In your table above it looks to me that just the coordinate type is the main difference. And the initialization results just in a 'small' difference.  
  
I use doubles. And that should work (even float should work IMO...). The area results of around 2 that you get for long doubles look OK to me (though I don't understand the difference, and how a minor perturbation can lead to 10% of a difference in results - I don't get that yet).  
  
I will do some comparisons with double/long double later, and also check the perimeter.

---

## Comment 3

> Username: vissarion  
> Created at: 2018-12-18 12:26:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-448203482  

You are right, I verify that the geometries are fine. It seems that the inaccuracy comes from summing up values that are very close to each other here : https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/geographic/area.hpp#L186   
That is why computation with `long double` overcomes the inaccuracy. On the other hand, Kahan summation didn't seem to help. Needs further investigation.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2018-12-18 18:13:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-448316681  

Hi @vissarion thanks. I now tried with `long double` and it is better - but also not perfect yet...  
  
Below my new output. As you can see the area is more in the range. But it is still unusuable...  
  
I now also added perimeter and that looks fine, for `long double`. And also for `double` it is reasonable (but not as good as their projected values).  
  
  
Output for long double:  
```  
numpoints system numpoints+1 area perimeter system numpoints+1 area perimeter  
5  geographic  6   -8.70934   5.87787  projected  6   2.3767   5.87668  
6  geographic  7   -9.605667940099227   6.000014405269862  projected  7   2.597044063790236   5.998808060343662  
7  geographic  8   -28.6840258191935   6.074389874202544  projected  8   2.735323084663833   6.073165633442998  
8  geographic  9   11.04197348580413   6.122952157160927  projected  9   2.827303468897298   6.121718560789472  
9  geographic  10   -7.609848928965851   6.156385383740395  projected  10   2.891395110484154   6.155139576603446  
10  geographic  11   -17.09551017365113   6.180361705603756  projected  11   2.937758704316366   6.17911212344289  
11  geographic  12   -4.66994831909932   6.198135973761448  projected  12   2.972343191977416   6.196884953050459  
12  geographic  13   31.85406111249817   6.211662920149589  projected  13   2.998808179052503   6.210423096128881  
13  geographic  14   -48.36471198818704   6.222224786814641  projected  14   3.019500574460835   6.220971190297066  
14  geographic  15   54.35439501514531   6.230600659644504  projected  15   3.035979579948616   6.229348404247994  
15  geographic  16   -20.65227759466741   6.237356143066346  projected  16   3.049312928953441   6.236111632974661  
16  geographic  17   -13.85074591323568   6.242891438249192  projected  17   3.060251218981648   6.241650113948441  
17  geographic  18   -73.97049289480156   6.24752317305389  projected  18   3.069334310953309   6.246242500893913  
18  geographic  19   -28.63149905632986   6.25131874333177  projected  19   3.07695840705037   6.250092524928219  
19  geographic  20   34.77809698644213   6.254614257579379  projected  20   3.083419511918692   6.253351917263175  
20  geographic  21   56.66763616728842   6.257391110435357  projected  21   3.088942300268172   6.256135532030151  
21  geographic  22   -142.6508088952368   6.259828080230406  projected  22   3.093699797324916   6.258531634024919  
22  geographic  23   41.29627763257787   6.261880830127452  projected  23   3.097826950365743   6.260608925253983  
23  geographic  24   44.21540304844772   6.263694919058841  projected  24   3.101430263120164   6.262421541250442  
24  geographic  25   148.2382582753415   6.265246057958701  projected  25   3.104594677340629   6.264012592198701  
25  geographic  26   -81.31339971108426   6.266732608215863  projected  26   3.107388616071603   6.26541676566701  
26  geographic  27   -46.7369579968207   6.267942912965663  projected  27   3.109867678206228   6.266662214475424  
27  geographic  28   -15.21189752858295   6.269056176908831  projected  28   3.112077415401018   6.26777197954567  
28  geographic  29   -6.397964923270046   6.26998728525905  projected  29   3.114055449885655   6.26876508146113  
29  geographic  30   -130.1152857882636   6.270888134743907  projected  30   3.115833052288053   6.269657312203098  
30  geographic  31   -11.47674017764075   6.271697263318989  projected  31   3.117436394232755   6.270461879818648  
31  geographic  32   6.880351493295484   6.272395970870092  projected  32   3.118887515793631   6.271189901466996  
32  geographic  33   101.1286344815155   6.273091237410482  projected  33   3.12020508393789   6.271850788937381  
33  geographic  34   50.09235745211329   6.273748100630176  projected  34   3.121404985066874   6.272452544650028  
34  geographic  35   -124.6317943026502   6.274227133920037  projected  35   3.122500820942832   6.273002021307129  
35  geographic  36   -43.07674325804758   6.274725652958071  projected  36   3.123504277623624   6.273505100582522  
36  geographic  37   77.96110927570538   6.275227769598489  projected  37   3.124425451146408   6.273966863875912  
37  geographic  38   93.88006571831122   6.275714758140596  projected  38   3.125273093441592   6.274391714174344  
38  geographic  39   68.48816641905242   6.276038073775148  projected  39   3.126054823096638   6.274783481943314  
39  geographic  40   -39.92998385169993   6.276431653077836  projected  40   3.126777296911314   6.275145513549985  
40  geographic  41   113.7640605137189   6.276636867917465  projected  41   3.127446355162419   6.27548074536756  
41  geographic  42   71.93103433272893   6.277014318975496  projected  42   3.128067133469131   6.275791757293996  
42  geographic  43   110.9793271047548   6.277307831640422  projected  43   3.128644166934009   6.276080828097051  
43  geographic  44   117.7244887709063   6.277602084472294  projected  44   3.129181469495279   6.276349973633893  
44  geographic  45   293.2099410003796   6.277809590166146  projected  45   3.12968260750813   6.276600985915282  
45  geographic  46   -146.8255608281281   6.278018187354725  projected  46   3.130150750192797   6.276835454094177  
46  geographic  47   -55.85972673204054   6.278335461919328  projected  47   3.130588734272351   6.277054803584052  
47  geographic  48   53.87766418303545   6.278610416958765  projected  48   3.13099909205414   6.277260304023793  
48  geographic  49   144.2215191312397   6.278661087707991  projected  49   3.131384101293179   6.277453099946584  
49  geographic  50   -345.4872121693842   6.27898300414949  projected  50   3.13174580628413   6.277634216836573  
50  geographic  51   -43.76808189643426   6.279022799439124  projected  51   3.132086049988629   6.277804578903483  
```  
  
Output for double  
```  
5  geographic  6   -7106.98   5.87145  projected  6   2.3767   5.87668  
6  geographic  7   9877.873026939991   5.990441149585183  projected  7   2.59704417915782   5.998808193597772  
7  geographic  8   -51940.44953270301   6.076608045950909  projected  8   2.735323085114942   6.073165633977603  
8  geographic  9   -15801.34382348935   6.123263339124103  projected  9   2.827303563128226   6.121718662777818  
9  geographic  10   -28607.43220085142   6.153498692251656  projected  10   2.891395106998971   6.155139572854291  
10  geographic  11   4168.449447946321   6.179248947943594  projected  11   2.937758780579315   6.179112203671658  
11  geographic  12   144878.6498449351   6.157551484274717  projected  12   2.97234318849587   6.196884949366017  
12  geographic  13   -28710.3576541802   6.243436350713854  projected  13   2.998808245720284   6.210423165152673  
13  geographic  14   13597.64092986423   6.248798925754039  projected  14   3.019500571803292   6.220971187512639  
14  geographic  15   -86283.87965229033   6.2561429224446  projected  15   3.035979636755656   6.229348462560575  
15  geographic  16   34802.96352132662   6.206895958733149  projected  16   3.049312928096697   6.236111632198725  
16  geographic  17   49095.97289276295   6.238712639300454  projected  17   3.060251271916059   6.241650167907099  
17  geographic  18   73707.87018695462   6.223765623920101  projected  18   3.069334308874204   6.246242498768325  
18  geographic  19   -17501.06691975416   6.288031507884794  projected  19   3.076958450678831   6.250092569270165  
19  geographic  20   11355.60807726318   6.260196866396992  projected  20   3.08341950580143   6.25335191104325  
20  geographic  21   54750.68440883196   6.174727768988833  projected  21   3.088942338168636   6.256135570454799  
21  geographic  22   -91423.81211860616   6.28608924130507  projected  22   3.093699793505039   6.258531630092383  
22  geographic  23   -40560.27572535454   6.208167131811362  projected  23   3.097826987302142   6.260608962512189  
23  geographic  24   -149152.3787578737   6.134579246128111  projected  24   3.101430254951083   6.262421533036529  
24  geographic  25   189158.8023008354   6.315632739554544  projected  25   3.104594711631762   6.264012626793592  
25  geographic  26   253645.7206160811   6.17313195148979  projected  26   3.107388613682815   6.265416763258954  
26  geographic  27   19394.72616995342   6.314308947007198  projected  27   3.109867707887588   6.266662244428333  
27  geographic  28   25803.50541588549   6.226280367672984  projected  28   3.112077415569274   6.267771979632485  
28  geographic  29   -289165.7023337696   6.42299700821913  projected  29   3.114055478587034   6.268765110350144  
29  geographic  30   138687.6766455288   6.277061288152301  projected  30   3.115833049064349   6.269657308967967  
30  geographic  31   133580.832379287   6.382206571971186  projected  31   3.117436420543072   6.270461906161152  
31  geographic  32   -154021.4551534558   6.119565526811111  projected  32   3.11888751231163   6.271189898033787  
32  geographic  33   65016.62865333557   6.2142592428263  projected  33   3.120205110878032   6.271850816016245  
33  geographic  34   700411.0785136138   6.213222877295266  projected  34   3.121404981123305   6.272452540676222  
34  geographic  35   -41061.30295670583   6.178680557974025  projected  35   3.122500844603564   6.273002045068829  
35  geographic  36   -549351.9575878523   6.374040143397463  projected  36   3.123504272464515   6.273505095333792  
36  geographic  37   -22154.422378672   6.17649265244964  projected  37   3.124425472392659   6.27396688534256  
37  geographic  38   15854.78040639361   6.472128723775513  projected  38   3.125273089965503   6.27439171068878  
38  geographic  39   171666.0336067806   6.283664699602097  projected  39   3.126054841625546   6.27478350054973  
39  geographic  40   -471754.6958557257   6.073149205169562  projected  40   3.126777293243322   6.275145509904249  
40  geographic  41   -98102.50219920019   5.799508218696191  projected  41   3.12744637304354   6.275480763371528  
41  geographic  42   -296735.0928373209   5.934658108771179  projected  42   3.128067131410489   6.275791755199044  
42  geographic  43   -409594.8254779964   6.140941248298602  projected  43   3.128644183363349   6.276080844577871  
43  geographic  44   84753.42718479398   5.933002239498226  projected  44   3.129181464903468   6.276349969081677  
44  geographic  45   284775.5204423611   5.902864570947456  projected  45   3.129682623452709   6.276601002008998  
45  geographic  46   -82284.61634465648   5.970360164674502  projected  46   3.130150745918627   6.276835449649665  
46  geographic  47   -702019.7854401803   6.186378752826677  projected  47   3.130588747509023   6.277054816761917  
47  geographic  48   -933381.192933112   6.216717190584058  projected  48   3.130999090044384   6.277260301957213  
48  geographic  49   372651.5107663177   6.202817750333533  projected  49   3.131384117016069   6.277453115733204  
49  geographic  50   -642122.396644773   6.500462042484167  projected  50   3.131745803445483   6.277634213933462  
50  geographic  51   -1615660.075003426   6.219329432941888  projected  51   3.132086063875477   6.277804592790042  
```

---

## Comment 5

> Username: barendgehrels  
> Created at: 2018-12-18 18:29:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-448321847  

And indeed, if I use a buffer distance of 100 meter instead of 1 meter, then coordinates are further apart, and I get a neat output, and also much better for `double`  
  
Output for `long double`  
```  
5  geographic  6   24406.7   587.787  projected  6   23767   587.668  
10  geographic  11   29389.3917650142   618.0358232388136  projected  11   29377.58735880721   617.9112156599526  
15  geographic  16   30505.42137202255   623.7369244367449  projected  16   30493.12967984087   623.6111672907244  
20  geographic  21   30902.35272895053   625.7397181644264  projected  21   30889.42341354454   625.6135573623785  
25  geographic  26   31088.36394180376   626.6680287578648  projected  26   31073.8865827361   626.5416808175752  
30  geographic  31   31185.06358378872   627.1726429540689  projected  31   31174.36438939671   627.0461924742734  
35  geographic  36   31248.2589853291   627.4770254838768  projected  36   31235.04324843551   627.3505147949721  
40  geographic  41   31287.61311179867   627.6746299245902  projected  41   31274.46400490234   627.548079086611  
45  geographic  46   31312.62648794206   627.8101281412048  projected  46   31301.50797059848   627.683550099199  
50  geographic  51   31335.88638699717   627.9070597607381  projected  51   31320.86095820805   627.7804624773828  
```  
  
Output for `double`  
  
```  
5  geographic  6   24424.5   587.787  projected  6   23767   587.668  
10  geographic  11   28954.61113643066   618.0358844756693  projected  11   29377.58735513408   617.9112156213394  
15  geographic  16   30467.21497418519   623.7366198510647  projected  16   30493.12967984623   623.6111672908565  
20  geographic  21   29999.62650359623   625.7399310790181  projected  21   30889.4234114656   625.6135573413259  
25  geographic  26   32533.4898053848   626.6691447541973  projected  26   31073.88658259891   626.5416808161938  
30  geographic  31   28521.01030769165   627.1729623150326  projected  31   31174.36438796759   627.0461924598916  
35  geographic  36   26814.83778598347   627.4765069980294  projected  36   31235.0432483115   627.350514793699  
40  geographic  41   26692.62545923042   627.6760558573422  projected  41   31274.46400359846   627.5480790734821  
45  geographic  46   23815.63281618918   627.8084416608789  projected  46   31301.50797025645   627.683550095775  
50  geographic  51   33650.51412680989   627.90694786202  projected  51   31320.86095703265   627.7804624655969  
```

---

## Comment 6

> Username: awulkiew  
> Created at: 2018-12-18 19:13:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-448335920  

@barendgehrels Out of curiosity, you're using `andoyer` (so for direct this is `thomas` without the 2nd degree approximation), correct? Have you tried different FormulaPolicies too? In case we didn't know where is the issue this could help to localize it. If the accuracy was low for all geodesic formulas we'd know that the error is in the area.  
  
@vissarion I'm also curious if maybe the `SeriesOrder` in area is to blame, i.e. are we sure that the order in area should be the same as in geodesic formulas? Area is squared length after all. I don't know what's the formula in area used for (I haven't looked so this is a wild guess) but if it takes some number of distances and returns the area is it possible that we need greater `SeriesOrder` to get comparable accuracy?

---

## Comment 7

> Username: barendgehrels  
> Created at: 2018-12-18 20:17:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-448355811  

@awulkiew yes, for buffer-generation I'm using the Andoyer indeed, by specifying `typename FormulaPolicy = strategy::andoyer` and I could change that. But the generation on itself seems OK now, as long as I don't touch the 180 degrees.  
  
For area I also use the default, by calling: `bg::area(buffer)` - so maybe I should specify the strategy to get better results.

---

## Comment 8

> Username: vissarion  
> Created at: 2018-12-19 12:39:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-448581667  

@awulkiew `SeriesOrder` does not change the behaviour. Since the problem is on short segments (where the ellipsoidal term is small anyway) we can just avoid it. To be more clear the algorithm adds a spherical and an ellipsoidal term so I propose to avoid the latter for short segments. @barendgehrels how are your examples behaviour with spherical CS?

---

## Comment 9

> Username: barendgehrels  
> Created at: 2018-12-19 14:57:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-448624152  

@vissarion : they behave well! Here the new table, using `double`, with the spherical area right after the geographic area, calculated by `bg::area(buffer, bg::strategy::area::spherical<>(6371228.0))`  
  
```  
5  geographic  6   -7106.98   2.36845   5.87145  projected  6   2.3767   5.87668  
10  geographic  11   4168.449447946321   2.927570082965481   6.179248947943594  projected  11   2.937758780579315   6.179112203671658  
15  geographic  16   34802.96352132662   3.038737335821288   6.206895958733149  projected  16   3.049312928096697   6.236111632198725  
20  geographic  21   54750.68440883196   3.078229307965614   6.174727768988833  projected  21   3.088942338168636   6.256135570454799  
25  geographic  26   253645.7206160811   3.096611607288448   6.17313195148979  projected  26   3.107388613682815   6.265416763258954  
30  geographic  31   133580.832379287   3.106624562744787   6.382206571971186  projected  31   3.117436420543072   6.270461906161152  
35  geographic  36   -549351.9575878523   3.112671374623749   6.374040143397463  projected  36   3.123504272464515   6.273505095333792  
40  geographic  41   -98102.50219920019   3.116599802490512   5.799508218696191  projected  41   3.12744637304354   6.275480763371528  
45  geographic  46   -82284.61634465648   3.119294795856522   5.970360164674502  projected  46   3.130150745918627   6.276835449649665  
50  geographic  51   -1615660.075003426   3.121223396694027   6.219329432941888  projected  51   3.132086063875477   6.277804592790042  
```

---

## Comment 10

> Username: vissarion  
> Created at: 2018-12-20 11:20:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-448962977  

@barendgehrels can we find the threshold at which the geographic formula (as is) is inaccurate? As you tested for 1m is inaccurate but for 100m is fine.

---

## Comment 11

> Username: barendgehrels  
> Created at: 2018-12-20 17:20:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-449072688  

Indeed, I can probably get such a threshold, in meters, for you. I try to get it tonight.

---

## Comment 12

> Username: barendgehrels  
> Created at: 2018-12-20 18:25:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-449091505  

@vissarion : these are my found values for the radius where area calculations are stable:  
Float: 19080  
Double: 605  
Long double: 45  
  
So actually double was not yet fine at 100m, because the output areas were reasonable but not yet stable. My test increases the number of points in a circle, the area therefore should increase (until it is pi, for a radius of 1). If it decreases it is not stable.  
  
So therefore the limit is higher. For float it is quite high (half the earth?)... But most probably people will use at least double for geographic cs (?).  
  
I tested at 3 locations (one in Amsterdam, one at ~22 N, one at ~82N - but there is no much variation, I took the highest values of these three.  
  
So the found radius is the radius of a buffer (around a point) with 5 up to 50 points (increasingly). So that might be an appropriate initial measurement for the threshold. It is not the distance from point to point. But for 5 or 6 points it has the same order, and that is where I started.

---

## Comment 13

> Username: vissarion  
> Created at: 2019-01-07 14:49:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-451958850  

@barendgehrels Thanks for the feedback. Still I cannot understand those numbers. Maybe there are several issues but I tried to focus a bit on the affect of inverse formulas (i.e. andoyer, thomas, vincenty). They are used to calculate the azimuth at the beginning of area computation. Surprisingly, for the data I tried (your data for 1m buffer) thomas resulted to the higher accuracy while vincenty to the lowest. Could you try to your whole dataset so we have a better idea, or tell me how to reproduce your data and I will do the test. To be clear I tried the following  
```  
bg::strategy::area::geographic  
<  
  inverse_strategy,  
  5,  
  bg::srs::spheroid<CT>,  
  CT  
> str;  
bg::area(polygon, str);  
```  
Where `inverse_strategy` can be either `bg::strategy::andoyer`, `bg::strategy::thomas`, or `bg::strategy::vincenty`, `CT` is the number type, and `5` is the order of approximation (it does not affect the results in my case).

---

## Comment 14

> Username: barendgehrels  
> Created at: 2019-01-07 18:30:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-452033819  

Right, I see. I did not use it exactly like this. I started with bg::area(polygon) which obviously goes wrong (for my case). I will post a complete minimal scenario, one of these days. If necessary - I will substitute what you did first to verify my findings.

---

## Comment 15

> Username: barendgehrels  
> Created at: 2019-01-09 10:21:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-452645819  

Hi @vissarion - below is my new table, followed by the test program. In the table: first column is radius, then all kinds of areas with ratio to projected area (for now considered as the truth). First for float (f), then double (d) and then long double (e).  
  
Thomas performs the best. I didn't know that because I didn't try it before. For long double, it is perfect even for one meter! Float is usually bad - most probably we should promote that internally and it will be fixed. Based on the table below, if we promote all to long double and use thomas - it will work correctly...  
  
So (based on your hint, and I also changed my test program a bit) my findings are different now and mainly depend on used inverse_strategy.  
  
  
```  
testing for f  
1 geographic -2616650.022 (-55248.998) andoyer -2616650.015 (-55248.997) thomas -2616884.666 (-55253.952) vincenty -2616880.404 (-55253.862) spherical 47.162 (0.996) projected 47.361  
2 geographic 2044950.046 (18246.779) andoyer 2044950.041 (18246.779) thomas -8326487.667 (-74295.984) vincenty -8326396.453 (-74295.171) spherical 111.666 (0.996) projected 112.072  
4 geographic 1741861.440 (7072.469) andoyer 1741861.429 (7072.469) thomas 6661512.407 (27047.696) vincenty 6596567.215 (26784.000) spherical 245.457 (0.997) projected 246.288  
8 geographic -8468850.767 (-14662.330) andoyer -8468850.757 (-14662.330) thomas 578.399 (1.001) vincenty -3908.772 (-6.767) spherical 575.644 (0.997) projected 577.592  
16 geographic 20164620.681 (13140.102) andoyer 20164620.604 (13140.102) thomas 1533.261 (0.999) vincenty 18778.747 (12.237) spherical 1529.411 (0.997) projected 1534.586  
32 geographic 150640065.989 (32514.002) andoyer 150640065.466 (32514.002) thomas 4638.348 (1.001) vincenty 13597.633 (2.935) spherical 4617.457 (0.997) projected 4633.083  
64 geographic -306584731.272 (-19904.868) andoyer -306584730.490 (-19904.868) thomas 15417.078 (1.001) vincenty 15913.975 (1.033) spherical 15350.553 (0.997) projected 15402.500  
128 geographic -694773483.095 (-12455.078) andoyer -694773480.986 (-12455.078) thomas 55817.069 (1.001) vincenty 56655.112 (1.016) spherical 55594.213 (0.997) projected 55782.347  
256 geographic 190560276.129 (906.418) andoyer 190560275.143 (906.418) thomas 210317.872 (1.000) vincenty 210709.415 (1.002) spherical 209525.477 (0.997) projected 210234.526  
512 geographic 1178331674.125 (1444.692) andoyer 1178331670.301 (1444.692) thomas 815989.978 (1.000) vincenty 816433.473 (1.001) spherical 812877.278 (0.997) projected 815628.126  
1024 geographic -527570208.535 (-157.729) andoyer -527570208.727 (-157.729) thomas 3346138.595 (1.000) vincenty 3346159.365 (1.000) spherical 3333501.613 (0.997) projected 3344782.350  
2048 geographic 8959153318.996 (674.797) andoyer 8959153288.916 (674.797) thomas 13282138.011 (1.000) vincenty 13282195.756 (1.000) spherical 13232038.375 (0.997) projected 13276816.420  
4096 geographic -15045873900.053 (-287.173) andoyer -15045873851.174 (-287.173) thomas 52414219.343 (1.000) vincenty 52414204.389 (1.000) spherical 52216378.481 (0.997) projected 52393088.902  
8192 geographic -4675164620.233 (-22.239) andoyer -4675164605.840 (-22.239) thomas 210303212.277 (1.000) vincenty 210303227.115 (1.000) spherical 209509936.500 (0.997) projected 210219019.846  
16384 geographic 841593524.763 (1.002) andoyer 841593523.057 (1.002) thomas 840475852.740 (1.000) vincenty 840475842.658 (1.000) spherical 837302908.768 (0.997) projected 840137824.184  
  
testing for d  
1 geographic -590757.726 (-189078.175) andoyer -590757.725 (-189078.174) thomas 0.000 (0.000) vincenty -0.927 (-0.297) spherical 3.114 (0.997) projected 3.124  
2 geographic -75092.034 (-6008.497) andoyer -75092.034 (-6008.497) thomas 12.476 (0.998) vincenty -2.411 (-0.193) spherical 12.455 (0.997) projected 12.498  
4 geographic -44830.186 (-896.773) andoyer -44830.186 (-896.773) thomas 50.041 (1.001) vincenty -9.739 (-0.195) spherical 49.822 (0.997) projected 49.991  
8 geographic -45412.033 (-227.103) andoyer -45412.033 (-227.103) thomas 199.987 (1.000) vincenty -38.992 (-0.195) spherical 199.288 (0.997) projected 199.962  
16 geographic -2415.338 (-3.020) andoyer -2415.338 (-3.020) thomas 800.173 (1.000) vincenty -155.643 (-0.195) spherical 797.151 (0.997) projected 799.849  
32 geographic 12972.208 (4.055) andoyer 12972.208 (4.055) thomas 3200.669 (1.000) vincenty 13070.195 (4.085) spherical 3188.605 (0.997) projected 3199.396  
64 geographic 10564.452 (0.826) andoyer 10564.452 (0.826) thomas 12802.682 (1.000) vincenty 21033.070 (1.644) spherical 12754.422 (0.997) projected 12797.584  
128 geographic 48182.060 (0.941) andoyer 48182.060 (0.941) thomas 51210.939 (1.000) vincenty 50955.079 (0.995) spherical 51017.687 (0.997) projected 51190.336  
256 geographic 204964.881 (1.001) andoyer 204964.881 (1.001) thomas 204843.977 (1.000) vincenty 203901.637 (0.996) spherical 204070.754 (0.997) projected 204761.346  
512 geographic 819471.930 (1.001) andoyer 819471.930 (1.001) thomas 819375.949 (1.000) vincenty 819152.330 (1.000) spherical 816283.046 (0.997) projected 819045.416  
1024 geographic 3277740.667 (1.000) andoyer 3277740.670 (1.000) thomas 3277504.330 (1.000) vincenty 3277381.984 (1.000) spherical 3265132.427 (0.997) projected 3276181.924  
2048 geographic 13109957.429 (1.000) andoyer 13109957.440 (1.000) thomas 13110019.639 (1.000) vincenty 13110025.356 (1.000) spherical 13060531.615 (0.997) projected 13104729.856  
4096 geographic 52440457.107 (1.000) andoyer 52440457.149 (1.000) thomas 52440093.556 (1.000) vincenty 52440113.002 (1.000) spherical 52242141.106 (0.997) projected 52418938.059  
8192 geographic 209761676.873 (1.000) andoyer 209761677.040 (1.000) thomas 209760481.410 (1.000) vincenty 209760493.006 (1.000) spherical 208968671.714 (0.997) projected 209675922.902  
16384 geographic 839047232.375 (1.000) andoyer 839047233.041 (1.000) thomas 839042616.289 (1.000) vincenty 839042622.334 (1.000) spherical 835875386.941 (0.997) projected 838705402.291  
  
testing for e  
1 geographic 189.969 (60.801) andoyer 189.969 (60.801) thomas 3.126 (1.000) vincenty -0.608 (-0.195) spherical 3.114 (0.997) projected 3.124  
2 geographic 137.277 (10.984) andoyer 137.277 (10.984) thomas 12.503 (1.000) vincenty -16.970 (-1.358) spherical 12.455 (0.997) projected 12.498  
4 geographic 38.234 (0.765) andoyer 38.234 (0.765) thomas 50.011 (1.000) vincenty 67.352 (1.347) spherical 49.822 (0.997) projected 49.991  
8 geographic 209.906 (1.050) andoyer 209.906 (1.050) thomas 200.043 (1.000) vincenty 208.040 (1.040) spherical 199.288 (0.997) projected 199.962  
16 geographic 796.485 (0.996) andoyer 796.485 (0.996) thomas 800.172 (1.000) vincenty 790.812 (0.989) spherical 797.151 (0.997) projected 799.849  
32 geographic 3199.594 (1.000) andoyer 3199.594 (1.000) thomas 3200.688 (1.000) vincenty 3201.491 (1.001) spherical 3188.605 (0.997) projected 3199.396  
64 geographic 12797.747 (1.000) andoyer 12797.747 (1.000) thomas 12802.750 (1.000) vincenty 12804.544 (1.001) spherical 12754.422 (0.997) projected 12797.584  
128 geographic 51211.510 (1.000) andoyer 51211.510 (1.000) thomas 51211.000 (1.000) vincenty 51209.779 (1.000) spherical 51017.687 (0.997) projected 51190.336  
256 geographic 204845.098 (1.000) andoyer 204845.098 (1.000) thomas 204844.006 (1.000) vincenty 204843.414 (1.000) spherical 204070.754 (0.997) projected 204761.346  
512 geographic 819380.579 (1.000) andoyer 819380.580 (1.000) thomas 819376.053 (1.000) vincenty 819376.109 (1.000) spherical 816283.046 (0.997) projected 819045.416  
1024 geographic 3277522.367 (1.000) andoyer 3277522.370 (1.000) thomas 3277504.458 (1.000) vincenty 3277504.375 (1.000) spherical 3265132.427 (0.997) projected 3276181.924  
2048 geographic 13110091.522 (1.000) andoyer 13110091.532 (1.000) thomas 13110019.743 (1.000) vincenty 13110019.993 (1.000) spherical 13060531.615 (0.997) projected 13104729.856  
4096 geographic 52440380.626 (1.000) andoyer 52440380.668 (1.000) thomas 52440093.629 (1.000) vincenty 52440094.598 (1.000) spherical 52242141.107 (0.997) projected 52418938.060  
8192 geographic 209761629.539 (1.000) andoyer 209761629.705 (1.000) thomas 209760481.451 (1.000) vincenty 209760485.160 (1.000) spherical 208968671.714 (0.997) projected 209675922.902  
16384 geographic 839047208.844 (1.000) andoyer 839047209.511 (1.000) thomas 839042616.438 (1.000) vincenty 839042619.881 (1.000) spherical 835875386.940 (0.997) projected 838705402.291  
```  
  
And this is the standalone test program:  
```  
#include <boost/geometry.hpp>  
  
#include <boost/geometry/srs/projection.hpp>  
#include <boost/geometry/srs/transformation.hpp>  
#include <boost/geometry/strategies/transform/srs_transformer.hpp>  
  
#include <iostream>  
  
namespace boost { namespace geometry  
{  
  
template  
<  
    typename FormulaPolicy = strategy::andoyer,  
    typename Spheroid = srs::spheroid<double>,  
    typename CalculationType = void  
>  
class point_circle_geographic  
{  
public :  
    explicit point_circle_geographic(std::size_t count = 90)  
        : m_count((count < 3u) ? 3u : count)  
    {}  
  
    template  
    <  
        typename Point,  
        typename OutputRange,  
        typename Distance  
    >  
    inline void apply(Point const& point,  
                Distance const& buffer_distance,  
                OutputRange& output_range) const  
    {  
        typedef typename boost::range_value<OutputRange>::type output_point_type;  
  
        typedef typename select_most_precise  
            <  
                typename geometry::coordinate_type<Point>::type,  
                typename geometry::coordinate_type<output_point_type>::type,  
                CalculationType  
            >::type calculation_type;  
  
        typedef typename FormulaPolicy::template direct  
            <  
                calculation_type, true, false, false, false  
            > direct_t;  
  
        calculation_type const two_pi = geometry::math::two_pi<calculation_type>();  
  
        calculation_type const diff = two_pi / calculation_type(m_count);  
        // TODO: after calculation of some angles is corrected, we can start at 0.0  
        calculation_type angle = 0.001;  
  
        for (std::size_t i = 0; i < m_count; i++, angle += diff)  
        {  
            typename direct_t::result_type  
                dir_r = direct_t::apply(get_as_radian<0>(point), get_as_radian<1>(point),  
                                        buffer_distance, angle,  
                                        m_spheroid);  
            output_point_type p;  
            set_from_radian<0>(p, dir_r.lon2);  
            set_from_radian<1>(p, dir_r.lat2);  
            output_range.push_back(p);  
        }  
  
        {  
            // Close it:  
            const output_point_type p = output_range.front();  
            output_range.push_back(p);  
        }  
    }  
  
private :  
    std::size_t m_count;  
    Spheroid m_spheroid;  
};  
  
}}  
  
  
namespace bg = boost::geometry;  
  
template <typename PointType>  
void test(double radius, int point_count)  
{  
    typedef typename bg::select_most_precise  
        <  
            typename bg::coordinate_type<PointType>::type,  
            double  
        >::type ct;  
  
    typedef bg::model::polygon<PointType> polygon_type;  
  
    PointType center(4.9, 52.0);  
    polygon_type buffer;  
  
    // Generate point buffer  
    bg::point_circle_geographic<> generator(point_count);  
    generator.apply(center, radius, bg::exterior_ring(buffer));  
  
    // Make a projection (~equal area)  
    typedef bg::projections::parameters<ct> parameters_type;  
    typedef bg::projections::sterea_ellipsoid<ct, parameters_type> projection_type;  
    bg::srs::detail::proj4_parameters params( "+lat_0=52.15616055555555 +lon_0=5.38763888888889 +k=0.9999079 +x_0=155000 +y_0=463000 +ellps=bessel +units=m");  
    parameters_type par = bg::projections::detail::pj_init<ct>(params);  
    bg::strategy::transform::srs_forward_transformer<projection_type> transformer(params, par);  
  
    typedef bg::model::d2::point_xy<ct> cartesian_point_type;  
    typedef bg::model::polygon<cartesian_point_type> cartesian_polygon_type;  
    cartesian_polygon_type cart_buffer;  
  
    bg::transform(buffer, cart_buffer, transformer);  
  
    // Calculate various areas  
    bg::strategy::area::geographic  
    <  
      bg::strategy::vincenty, 5, bg::srs::spheroid<ct>, ct  
    > vincenty_strategy;  
  
    bg::strategy::area::geographic  
    <  
      bg::strategy::andoyer, 5, bg::srs::spheroid<ct>, ct  
    > andoyer_strategy;  
  
    bg::strategy::area::geographic  
    <  
      bg::strategy::thomas, 5, bg::srs::spheroid<ct>, ct  
    > thomas_strategy;  
  
    const ct default_area = bg::area(buffer);  
    const ct spherical_area = bg::area(buffer, bg::strategy::area::spherical<>(6371228.0));  
    const ct cart_area = bg::area(cart_buffer);  
    const ct andoyer_area = bg::area(buffer, andoyer_strategy);  
    const ct thomas_area = bg::area(buffer, thomas_strategy);  
    const ct vincenty_area = bg::area(buffer, vincenty_strategy);  
  
    // Report  
    std::cout << int(radius)  
            << std::fixed << std::setprecision(3)  
            << " geographic " << default_area << " (" << default_area / cart_area << ")"  
            << " andoyer " << andoyer_area << " (" << andoyer_area / cart_area << ")"  
            << " thomas " << thomas_area << " (" << thomas_area / cart_area << ")"  
            << " vincenty " << vincenty_area << " (" << vincenty_area / cart_area << ")"  
            << " spherical " << spherical_area << " (" << spherical_area / cart_area << ")"  
            << " projected " << cart_area  
            << std::endl;  
  
}  
  
template <typename CoordinateType>  
void test_all(int point_count)  
{  
    std::cout << "testing for "  << typeid(CoordinateType).name() << std::endl;  
    double radius = 1.0;  
    for (int i = 0; i < 15; i++, radius *= 2.0)  
    {  
        test<bg::model::point<CoordinateType, 2, bg::cs::geographic<bg::degree> > >(radius, point_count);  
    }  
}  
  
int main(int, char* [])  
{  
    test_all<float>(36);  
    test_all<double>(36);  
    test_all<long double>(36);  
    return 0;  
}  
```

---

## Comment 16

> Username: vissarion  
> Created at: 2019-01-09 14:22:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-452713129  

@barendgehrels  thanks for the results and code. Below I added one more formula, named `series` below, implemented here: https://github.com/boostorg/geometry/pull/500 (with some modifications to use radians instead of degrees). This is expected to be the most accurate method when used with `long double` and "verifies" the correctness of `thomas` formula.   
  
Two comments here: a) the results are expected to be different for longer distances (see https://github.com/boostorg/geometry/pull/431#issuecomment-367357573), b) `vincenty` appears to be less accurate than (the more efficient) `thomas` in this dataset which is at least to my understanding unexpected.  
Both comments are written with distance computation in mind but here we compute azimuths so it needs further investigation.  
```  
testing for f  
1 geographic -2616650.022 (-55248.998) andoyer -2616650.015 (-55248.997) thomas -2616884.666 (-55253.952) vincenty -2616880.404 (-55253.862) series 884342.911 (18672.371) spherical 47.162 (0.996) projected 47.361  
2 geographic 2044950.046 (18246.779) andoyer 2044950.041 (18246.779) thomas -8326487.667 (-74295.984) vincenty -8326396.453 (-74295.171) series -772899.346 (-6896.463) spherical 111.666 (0.996) projected 112.072  
4 geographic 1741861.440 (7072.469) andoyer 1741861.429 (7072.469) thomas 6661512.407 (27047.696) vincenty 6596567.215 (26784.000) series 1509817.845 (6130.304) spherical 245.457 (0.997) projected 246.288  
8 geographic -8468850.767 (-14662.330) andoyer -8468850.757 (-14662.330) thomas 578.399 (1.001) vincenty -3908.772 (-6.767) series 1972339.431 (3414.760) spherical 575.644 (0.997) projected 577.592  
16 geographic 20164620.681 (13140.102) andoyer 20164620.604 (13140.102) thomas 1533.261 (0.999) vincenty 18778.747 (12.237) series -1513872.171 (-986.502) spherical 1529.411 (0.997) projected 1534.586  
32 geographic 150640065.989 (32514.002) andoyer 150640065.466 (32514.002) thomas 4638.348 (1.001) vincenty 13597.633 (2.935) series -183191.011 (-39.540) spherical 4617.457 (0.997) projected 4633.083  
64 geographic -306584731.272 (-19904.868) andoyer -306584730.490 (-19904.868) thomas 15417.078 (1.001) vincenty 15913.975 (1.033) series 625519.996 (40.612) spherical 15350.553 (0.997) projected 15402.500  
128 geographic -694773483.095 (-12455.078) andoyer -694773480.986 (-12455.078) thomas 55817.066 (1.001) vincenty 56655.112 (1.016) series 294780.677 (5.284) spherical 55594.213 (0.997) projected 55782.347  
256 geographic 190560276.129 (906.418) andoyer 190560275.143 (906.418) thomas 210317.872 (1.000) vincenty 210709.415 (1.002) series -165180.081 (-0.786) spherical 209525.477 (0.997) projected 210234.526  
512 geographic 1178331674.125 (1444.692) andoyer 1178331670.301 (1444.692) thomas 815989.978 (1.000) vincenty 816433.473 (1.001) series -158161.915 (-0.194) spherical 812877.278 (0.997) projected 815628.126  
1024 geographic -527570208.535 (-157.729) andoyer -527570208.727 (-157.729) thomas 3346138.595 (1.000) vincenty 3346159.365 (1.000) series 2977999.064 (0.890) spherical 3333501.613 (0.997) projected 3344782.350  
2048 geographic 8959153318.996 (674.797) andoyer 8959153288.916 (674.797) thomas 13282138.011 (1.000) vincenty 13282195.756 (1.000) series 13503619.322 (1.017) spherical 13232038.375 (0.997) projected 13276816.420  
4096 geographic -15045873900.053 (-287.173) andoyer -15045873851.174 (-287.173) thomas 52414219.343 (1.000) vincenty 52414204.389 (1.000) series 52647596.389 (1.005) spherical 52216378.481 (0.997) projected 52393088.902  
8192 geographic -4675164620.233 (-22.239) andoyer -4675164605.840 (-22.239) thomas 210303212.277 (1.000) vincenty 210303227.115 (1.000) series 210596896.520 (1.002) spherical 209509936.500 (0.997) projected 210219019.846  
16384 geographic 841593524.763 (1.002) andoyer 841593523.057 (1.002) thomas 840475852.736 (1.000) vincenty 840475842.661 (1.000) series 840286070.892 (1.000) spherical 837302908.768 (0.997) projected 840137824.184  
testing for d  
1 geographic -590757.714 (-189078.171) andoyer -590757.712 (-189078.170) thomas 0.000 (0.000) vincenty -0.928 (-0.297) series 3.134 (1.003) spherical 3.114 (0.997) projected 3.124  
2 geographic -109060.586 (-8726.494) andoyer -109060.586 (-8726.494) thomas 12.474 (0.998) vincenty -2.411 (-0.193) series 12.659 (1.013) spherical 12.455 (0.997) projected 12.498  
4 geographic -44830.186 (-896.773) andoyer -44830.186 (-896.773) thomas 50.041 (1.001) vincenty -9.739 (-0.195) series 50.082 (1.002) spherical 49.822 (0.997) projected 49.991  
8 geographic -45412.033 (-227.103) andoyer -45412.033 (-227.103) thomas 199.987 (1.000) vincenty -38.992 (-0.195) series 199.974 (1.000) spherical 199.288 (0.997) projected 199.962  
16 geographic -2415.338 (-3.020) andoyer -2415.338 (-3.020) thomas 800.173 (1.000) vincenty -155.643 (-0.195) series 800.143 (1.000) spherical 797.151 (0.997) projected 799.849  
32 geographic 12972.208 (4.055) andoyer 12972.208 (4.055) thomas 3200.669 (1.000) vincenty 13070.195 (4.085) series 3200.655 (1.000) spherical 3188.605 (0.997) projected 3199.396  
64 geographic 10564.452 (0.826) andoyer 10564.452 (0.826) thomas 12802.682 (1.000) vincenty 21033.070 (1.644) series 12802.642 (1.000) spherical 12754.422 (0.997) projected 12797.584  
128 geographic 48182.060 (0.941) andoyer 48182.060 (0.941) thomas 51210.939 (1.000) vincenty 50955.078 (0.995) series 51211.032 (1.000) spherical 51017.687 (0.997) projected 51190.336  
256 geographic 204964.881 (1.001) andoyer 204964.881 (1.001) thomas 204843.977 (1.000) vincenty 203901.637 (0.996) series 204843.926 (1.000) spherical 204070.754 (0.997) projected 204761.346  
512 geographic 819471.930 (1.001) andoyer 819471.930 (1.001) thomas 819375.948 (1.000) vincenty 819152.330 (1.000) series 819375.861 (1.000) spherical 816283.046 (0.997) projected 819045.416  
1024 geographic 3277740.667 (1.000) andoyer 3277740.670 (1.000) thomas 3277504.330 (1.000) vincenty 3277381.987 (1.000) series 3277504.419 (1.000) spherical 3265132.427 (0.997) projected 3276181.924  
2048 geographic 13109957.429 (1.000) andoyer 13109957.440 (1.000) thomas 13110019.639 (1.000) vincenty 13110025.356 (1.000) series 13110019.757 (1.000) spherical 13060531.615 (0.997) projected 13104729.856  
4096 geographic 52440457.103 (1.000) andoyer 52440457.145 (1.000) thomas 52440093.556 (1.000) vincenty 52440113.002 (1.000) series 52440093.394 (1.000) spherical 52242141.106 (0.997) projected 52418938.059  
8192 geographic 209761676.873 (1.000) andoyer 209761677.039 (1.000) thomas 209760481.410 (1.000) vincenty 209760493.006 (1.000) series 209760481.321 (1.000) spherical 208968671.714 (0.997) projected 209675922.902  
16384 geographic 839047232.375 (1.000) andoyer 839047233.041 (1.000) thomas 839042616.289 (1.000) vincenty 839042622.334 (1.000) series 839042616.172 (1.000) spherical 835875386.941 (0.997) projected 838705402.291  
testing for e  
1 geographic 189.969 (60.801) andoyer 189.969 (60.801) thomas 3.126 (1.000) vincenty -0.608 (-0.195) series 3.126 (1.000) spherical 3.114 (0.997) projected 3.124  
2 geographic 137.277 (10.984) andoyer 137.277 (10.984) thomas 12.503 (1.000) vincenty -16.970 (-1.358) series 12.503 (1.000) spherical 12.455 (0.997) projected 12.498  
4 geographic 38.234 (0.765) andoyer 38.234 (0.765) thomas 50.011 (1.000) vincenty 67.352 (1.347) series 50.011 (1.000) spherical 49.822 (0.997) projected 49.991  
8 geographic 209.906 (1.050) andoyer 209.906 (1.050) thomas 200.043 (1.000) vincenty 208.040 (1.040) series 200.043 (1.000) spherical 199.288 (0.997) projected 199.962  
16 geographic 796.485 (0.996) andoyer 796.485 (0.996) thomas 800.172 (1.000) vincenty 790.812 (0.989) series 800.172 (1.000) spherical 797.151 (0.997) projected 799.849  
32 geographic 3199.594 (1.000) andoyer 3199.594 (1.000) thomas 3200.688 (1.000) vincenty 3201.491 (1.001) series 3200.688 (1.000) spherical 3188.605 (0.997) projected 3199.396  
64 geographic 12797.747 (1.000) andoyer 12797.747 (1.000) thomas 12802.750 (1.000) vincenty 12804.544 (1.001) series 12802.750 (1.000) spherical 12754.422 (0.997) projected 12797.584  
128 geographic 51211.510 (1.000) andoyer 51211.510 (1.000) thomas 51211.000 (1.000) vincenty 51209.779 (1.000) series 51211.000 (1.000) spherical 51017.687 (0.997) projected 51190.336  
256 geographic 204845.098 (1.000) andoyer 204845.098 (1.000) thomas 204844.006 (1.000) vincenty 204843.414 (1.000) series 204844.006 (1.000) spherical 204070.754 (0.997) projected 204761.346  
512 geographic 819380.579 (1.000) andoyer 819380.580 (1.000) thomas 819376.053 (1.000) vincenty 819376.109 (1.000) series 819376.053 (1.000) spherical 816283.046 (0.997) projected 819045.416  
1024 geographic 3277522.367 (1.000) andoyer 3277522.370 (1.000) thomas 3277504.458 (1.000) vincenty 3277504.375 (1.000) series 3277504.457 (1.000) spherical 3265132.427 (0.997) projected 3276181.924  
2048 geographic 13110091.522 (1.000) andoyer 13110091.532 (1.000) thomas 13110019.743 (1.000) vincenty 13110019.993 (1.000) series 13110019.739 (1.000) spherical 13060531.615 (0.997) projected 13104729.856  
4096 geographic 52440380.626 (1.000) andoyer 52440380.668 (1.000) thomas 52440093.629 (1.000) vincenty 52440094.598 (1.000) series 52440093.610 (1.000) spherical 52242141.107 (0.997) projected 52418938.060  
8192 geographic 209761629.539 (1.000) andoyer 209761629.705 (1.000) thomas 209760481.451 (1.000) vincenty 209760485.160 (1.000) series 209760481.384 (1.000) spherical 208968671.714 (0.997) projected 209675922.902  
16384 geographic 839047208.844 (1.000) andoyer 839047209.511 (1.000) thomas 839042616.438 (1.000) vincenty 839042619.881 (1.000) series 839042616.309 (1.000) spherical 835875386.940 (0.997) projected 838705402.291  
```

---

## Comment 17

> Username: barendgehrels  
> Created at: 2019-01-09 15:27:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-452736237  

Hi @vissarion, cool, the series is indeed better and perfect for both double and long double. But for float it behaves worse.  
Maybe we can make series that the default strategy for area calculations? And internally please promote to at least double to avoid large deviations? How is it performance-wise (w.r.t. speed)?  
That PR is not merged yet I think? What is blocking it?

---

## Comment 18

> Username: vissarion  
> Created at: 2019-01-10 14:02:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-453105890  

>Maybe we can make series that the default strategy for area calculations? How is it performance-wise (w.r.t. speed)?  
  
Roughly speaking for inverse problems (distance) andoyer is 2x faster than thomas which is 2x faster than vincenty which is faster than GeographicLib (that implements the series formulas we discuss). I should benchmark those formulas for this specific problem though.  
  
>That PR is not merged yet I think? What is blocking it?  
  
Apart from the degrees/radians issue discussed above I am ok with merging. There are some comments by @awulkiew unanswered to the PR page though.

---

## Comment 19

> Username: vissarion  
> Created at: 2019-01-22 10:54:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-456357242  

I posted some results on accuracy and performance for various strategies here: https://github.com/vissarion/geometry/wiki/Accuracy-and-performance-of-geographic-algorithms  
I would like to add one (or more) projection methods for area but I think the one used in the example code above is not the right one, *e.g.* it uses the `bessel` ellipsoid instead of `WGS84`.   
  
@awulkiew what projection method do you recommend for area computation?

---

## Comment 20

> Username: awulkiew  
> Created at: 2019-01-22 14:42:47 UTC  
> Updated at: 2019-01-22 14:43:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-456423891  

@vissarion The choice of projection depends on place on the globe and the usage of the map.  
  
For each region of the globe there are some standard ones, e.g. Barend above used this one: https://epsg.io/28992 for Netherlands. So this is a safe pick if you want to represent coordinates in Netherlands in a standard way.  
  
If you want to calculate areas at various places of the globe and want to compare them then you could choose one of the equal-area projections, e.g.:  
- aea - Albers Equal Area (https://proj4.org/operations/projections/aea.html)  
- cea - Equal Area Cylindrical (https://proj4.org/operations/projections/cea.html)  
- leac - Lambert Equal Area Conic (https://proj4.org/operations/projections/leac.html)  
- laea - Lambert Azimuthal Equal Area (https://proj4.org/operations/projections/laea.html)

---

## Comment 21

> Username: vissarion  
> Created at: 2019-01-23 15:04:23 UTC  
> Updated at: 2019-01-23 15:05:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/541#issuecomment-456835364  

@awulkiew thanks! I updated the benchmarks. As expected the right projection could be very accurate for short distances but as the distance increases (typically >100m) then projection method becomes inaccurate. Interestingly, there is no sole formula that attains the highest accuracy in all the tested cases.    
  
Returning to the current issue, there is a lot of freedom for the choice of default strategy for area depending on where we want to focus (e.g. do we want good accuracy for short or long distances?). Does it make sense to have more than one default depending on whether a segment is "long" or "short"?
