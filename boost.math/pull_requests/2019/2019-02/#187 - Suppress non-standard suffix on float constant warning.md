# #187 Suppress non-standard suffix on float constant warning [Open]

> Username: Kojoley  
> Created at: 2019-02-28 15:04:13 UTC  
> Updated at: 2022-10-29 17:13:15 UTC  
> Url: https://github.com/boostorg/math/pull/187  

Unfortunately there is no other way to suppress GCC pedantic warnings except to mark the header as system.  
  
```  
boost/math/constants/constants.hpp:264:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(half, 5.000000000000000000000000000000000000e-01, "5.00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:265:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(third, 3.333333333333333333333333333333333333e-01, "3.33333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333333e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:266:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(twothirds, 6.666666666666666666666666666666666666e-01, "6.66666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666667e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:267:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(two_thirds, 6.666666666666666666666666666666666666e-01, "6.66666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666667e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:268:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(sixth, 1.666666666666666666666666666666666666e-01, "1.66666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666667e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:269:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(three_quarters, 7.500000000000000000000000000000000000e-01, "7.50000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:270:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_two, 1.414213562373095048801688724209698078e+00, "1.41421356237309504880168872420969807856967187537694807317667973799073247846210703885038753432764157273501384623e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:271:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_three, 1.732050807568877293527446341505872366e+00, "1.73205080756887729352744634150587236694280525381038062805580697945193301690880003708114618675724857567562614142e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:272:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(half_root_two, 7.071067811865475244008443621048490392e-01, "7.07106781186547524400844362104849039284835937688474036588339868995366239231053519425193767163820786367506923115e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:273:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(ln_two, 6.931471805599453094172321214581765680e-01, "6.93147180559945309417232121458176568075500134360255254120680009493393621969694715605863326996418687542001481021e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:274:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(ln_ln_two, -3.665129205816643270124391582326694694e-01, "-3.66512920581664327012439158232669469454263447837105263053677713670561615319352738549455822856698908358302523045e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:275:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_ln_four, 1.177410022515474691011569326459699637e+00, "1.17741002251547469101156932645969963774738568938582053852252575650002658854698492680841813836877081106747157858e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:276:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(one_div_root_two, 7.071067811865475244008443621048490392e-01, "7.07106781186547524400844362104849039284835937688474036588339868995366239231053519425193767163820786367506923115e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:277:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(pi, 3.141592653589793238462643383279502884e+00, "3.14159265358979323846264338327950288419716939937510582097494459230781640628620899862803482534211706798214808651e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:278:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(half_pi, 1.570796326794896619231321691639751442e+00, "1.57079632679489661923132169163975144209858469968755291048747229615390820314310449931401741267105853399107404326e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:279:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(third_pi, 1.047197551196597746154214461093167628e+00, "1.04719755119659774615421446109316762806572313312503527365831486410260546876206966620934494178070568932738269550e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:280:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(sixth_pi, 5.235987755982988730771072305465838140e-01, "5.23598775598298873077107230546583814032861566562517636829157432051302734381034833104672470890352844663691347752e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:281:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(two_pi, 6.283185307179586476925286766559005768e+00, "6.28318530717958647692528676655900576839433879875021164194988918461563281257241799725606965068423413596429617303e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:282:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(two_thirds_pi, 2.094395102393195492308428922186335256e+00, "2.09439510239319549230842892218633525613144626625007054731662972820521093752413933241868988356141137865476539101e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:283:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(three_quarters_pi, 2.356194490192344928846982537459627163e+00, "2.35619449019234492884698253745962716314787704953132936573120844423086230471465674897102611900658780098661106488e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:284:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(four_thirds_pi, 4.188790204786390984616857844372670512e+00, "4.18879020478639098461685784437267051226289253250014109463325945641042187504827866483737976712282275730953078202e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:285:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(one_div_two_pi, 1.591549430918953357688837633725143620e-01, "1.59154943091895335768883763372514362034459645740456448747667344058896797634226535090113802766253085956072842727e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:286:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(one_div_root_two_pi, 3.989422804014326779399460599343818684e-01, "3.98942280401432677939946059934381868475858631164934657665925829670657925899301838501252333907306936430302558863e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:287:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_pi, 1.772453850905516027298167483341145182e+00, "1.77245385090551602729816748334114518279754945612238712821380778985291128459103218137495065673854466541622682362e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:288:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_half_pi, 1.253314137315500251207882642405522626e+00, "1.25331413731550025120788264240552262650349337030496915831496178817114682730392098747329791918902863305800498633e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:289:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_two_pi, 2.506628274631000502415765284811045253e+00, "2.50662827463100050241576528481104525300698674060993831662992357634229365460784197494659583837805726611600997267e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:290:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(log_root_two_pi, 9.189385332046727417803297364056176398e-01, "9.18938533204672741780329736405617639861397473637783412817151540482765695927260397694743298635954197622005646625e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:291:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(one_div_root_pi, 5.641895835477562869480794515607725858e-01, "5.64189583547756286948079451560772585844050629328998856844085721710642468441493414486743660202107363443028347906e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:292:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_one_div_pi, 5.641895835477562869480794515607725858e-01, "5.64189583547756286948079451560772585844050629328998856844085721710642468441493414486743660202107363443028347906e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:293:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(pi_minus_three, 1.415926535897932384626433832795028841e-01, "1.41592653589793238462643383279502884197169399375105820974944592307816406286208998628034825342117067982148086513e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:294:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(four_minus_pi, 8.584073464102067615373566167204971158e-01, "8.58407346410206761537356616720497115802830600624894179025055407692183593713791001371965174657882932017851913487e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:296:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(pi_pow_e, 2.245915771836104547342715220454373502e+01, "2.24591577183610454734271522045437350275893151339966922492030025540669260403991179123185197527271430315314500731e+01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:297:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(pi_sqr, 9.869604401089358618834490999876151135e+00, "9.86960440108935861883449099987615113531369940724079062641334937622004482241920524300177340371855223182402591377e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:298:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(pi_sqr_div_six, 1.644934066848226436472415166646025189e+00, "1.64493406684822643647241516664602518921894990120679843773555822937000747040320087383362890061975870530400431896e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:299:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(pi_cubed, 3.100627668029982017547631506710139520e+01, "3.10062766802998201754763150671013952022252885658851076941445381038063949174657060375667010326028861930301219616e+01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:300:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(cbrt_pi, 1.464591887561523263020142527263790391e+00, "1.46459188756152326302014252726379039173859685562793717435725593713839364979828626614568206782035382089750397002e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:301:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(one_div_cbrt_pi, 6.827840632552956814670208331581645981e-01, "6.82784063255295681467020833158164598108367515632448804042681583118899226433403918237673501922595519865685577274e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:302:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(e, 2.718281828459045235360287471352662497e+00, "2.71828182845904523536028747135266249775724709369995957496696762772407663035354759457138217852516642742746639193e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:303:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(exp_minus_half, 6.065306597126334236037995349911804534e-01, "6.06530659712633423603799534991180453441918135487186955682892158735056519413748423998647611507989456026423789794e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:304:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(exp_minus_one, 3.678794411714423215955237701614608674e-01, "3.67879441171442321595523770161460867445811131031767834507836801697461495744899803357147274345919643746627325277e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:305:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(e_pow_pi, 2.314069263277926900572908636794854738e+01, "2.31406926327792690057290863679485473802661062426002119934450464095243423506904527835169719970675492196759527048e+01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:306:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_e, 1.648721270700128146848650787814163571e+00, "1.64872127070012814684865078781416357165377610071014801157507931164066102119421560863277652005636664300286663776e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:307:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(log10_e, 4.342944819032518276511289189166050822e-01, "4.34294481903251827651128918916605082294397005803666566114453783165864649208870774729224949338431748318706106745e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:308:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(one_div_log10_e, 2.302585092994045684017991454684364207e+00, "2.30258509299404568401799145468436420760110148862877297603332790096757260967735248023599720508959829834196778404e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:309:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(ln_ten, 2.302585092994045684017991454684364207e+00, "2.30258509299404568401799145468436420760110148862877297603332790096757260967735248023599720508959829834196778404e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:310:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(degree, 1.745329251994329576923690768488612713e-02, "1.74532925199432957692369076848861271344287188854172545609719144017100911460344944368224156963450948221230449251e-02")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:311:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(radian, 5.729577951308232087679815481410517033e+01, "5.72957795130823208767981548141051703324054724665643215491602438612028471483215526324409689958511109441862233816e+01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:312:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(sin_one, 8.414709848078965066525023216302989996e-01, "8.41470984807896506652502321630298999622563060798371065672751709991910404391239668948639743543052695854349037908e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:313:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(cos_one, 5.403023058681397174009366074429766037e-01, "5.40302305868139717400936607442976603732310420617922227670097255381100394774471764517951856087183089343571731160e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:314:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(sinh_one, 1.175201193643801456882381850595600815e+00, "1.17520119364380145688238185059560081515571798133409587022956541301330756730432389560711745208962339184041953333e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:315:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(cosh_one, 1.543080634815243778477905620757061682e+00, "1.54308063481524377847790562075706168260152911236586370473740221471076906304922369896426472643554303558704685860e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:316:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(phi, 1.618033988749894848204586834365638117e+00, "1.61803398874989484820458683436563811772030917980576286213544862270526046281890244970720720418939113748475408808e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:317:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(ln_phi, 4.812118250596034474977589134243684231e-01, "4.81211825059603447497758913424368423135184334385660519661018168840163867608221774412009429122723474997231839958e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:318:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(one_div_ln_phi, 2.078086921235027537601322606117795767e+00, "2.07808692123502753760132260611779576774219226778328348027813992191974386928553540901445615414453604821933918634e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:319:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(euler, 5.772156649015328606065120900824024310e-01, "5.77215664901532860606512090082402431042159335939923598805767234884867726777664670936947063291746749514631447250e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:320:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(one_div_euler, 1.732454714600633473583025315860829681e+00, "1.73245471460063347358302531586082968115577655226680502204843613287065531408655243008832840219409928068072365714e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:321:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(euler_sqr, 3.331779238077186743183761363552442266e-01, "3.33177923807718674318376136355244226659417140249629743150833338002265793695756669661263268631715977303039565603e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:322:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(zeta_two, 1.644934066848226436472415166646025189e+00, "1.64493406684822643647241516664602518921894990120679843773555822937000747040320087383362890061975870530400431896e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:323:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(zeta_three, 1.202056903159594285399738161511449990e+00, "1.20205690315959428539973816151144999076498629234049888179227155534183820578631309018645587360933525814619915780e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:324:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(catalan, 9.159655941772190150546035149323841107e-01, "9.15965594177219015054603514932384110774149374281672134266498119621763019776254769479356512926115106248574422619e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:325:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(glaisher, 1.282427129100622636875342568869791727e+00, "1.28242712910062263687534256886979172776768892732500119206374002174040630885882646112973649195820237439420646120e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:326:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(khinchin, 2.685452001065306445309714835481795693e+00, "2.68545200106530644530971483548179569382038229399446295305115234555721885953715200280114117493184769799515346591e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:327:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(extreme_value_skewness, 1.139547099404648657492793019389846112e+00, "1.13954709940464865749279301938984611208759979583655182472165571008524800770607068570718754688693851501894272049e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:328:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(rayleigh_skewness, 6.311106578189371381918993515442277798e-01, "6.31110657818937138191899351544227779844042203134719497658094585692926819617473725459905027032537306794400047264e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:329:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(rayleigh_kurtosis, 3.245089300687638062848660410619754415e+00, "3.24508930068763806284866041061975441541706673178920936177133764493367904540874159051490619368679348977426462633e+00")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:330:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(rayleigh_kurtosis_excess, 2.450893006876380628486604106197544154e-01, "2.45089300687638062848660410619754415417066731789209361771337644933679045408741590514906193686793489774264626328e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:332:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(two_div_pi, 6.366197723675813430755350534900574481e-01, "6.36619772367581343075535053490057448137838582961825794990669376235587190536906140360455211065012343824291370907e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/math/constants/constants.hpp:333:3: warning: non-standard suffix on floating constant [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(root_two_div_pi, 7.978845608028653558798921198687637369e-01, "7.97884560802865355879892119868763736951717262329869315331851659341315851798603677002504667814613872860605117725e-01")  
   ^~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-02-28 15:10:22 UTC  
> Url: https://github.com/boostorg/math/pull/187#issuecomment-468308446  

Sigh, there is the same thing also in `boost/math/special_functions/log1p.hpp` and  `boost/math/special_functions/expm1.hpp`

---

## Comment 2

> Username: pabristow  
> Created_at: 2019-02-28 17:55:39 UTC  
> Url: https://github.com/boostorg/math/pull/187#issuecomment-468371931  

My initial reaction is to sigh and think:  
  
Patient: "Doctor it hurts when it do -pedantic",  
Doctor : "Don't do that!"  
  
But lets see what John thinks.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-02-28 18:01:57 UTC  
> Url: https://github.com/boostorg/math/pull/187#issuecomment-468374225  

There are people who have to use highest available warning levels because of field standards, recently there were messages on the mail list about this (again) and that Boost is too problematic in this scope so they have no other choice except to maintain their own fork of Boost.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-02-28 18:33:10 UTC  
> Url: https://github.com/boostorg/math/pull/187#issuecomment-468385178  

> Patient: "Doctor it hurts when it do -pedantic",  
> Doctor : "Don't do that!"  
>  
> But lets see what John thinks.  
  
We did use to be warning clean with -Wall -pedantic but those pesky compilers keep adding new warnings.  Some are useful, some not so much.  But yes, we should look at this.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-02-28 18:46:16 UTC  
> Url: https://github.com/boostorg/math/pull/187#issuecomment-468389678  

This is a very old problem. GCC seems to emits non-standard suffix warning for `Q` since its introduction. GCC 4.4.7 https://godbolt.org/z/OE7nfT

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-03-02 16:17:59 UTC  
> Url: https://github.com/boostorg/math/pull/187#issuecomment-468934737  

Confirmed that this is a problem, and that a very large number of headers are effected.  Note that the issue does not occur if you compile with `-std=c++XX -pedantic` only with `-std=gnu++XX -pedantic`.  It's temping to suggest that if you ask for GNU extensions and then ask to be warned of their use then you get what you deserve! ;)  However, I do have a fix being tested locally at present.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2019-03-02 18:37:54 UTC  
> Url: https://github.com/boostorg/math/pull/187#issuecomment-468947700  

> Note that the issue does not occur if you compile with `-std=c++XX -pedantic` only with `-std=gnu++XX -pedantic`. It's temping to suggest that if you ask for GNU extensions and then ask to be warned of their use then you get what you deserve! ;)  
  
The problem is that GCC has GNU extensions enabled by default, so I am getting errors with `b2 toolset=gcc warnings=all warnings-as-errors=on` =\

---
