# #717 Print convergence of tanh-sinh quadrature using BOOST_MATH_TANH_SINH_… [Closed]

> Username: NAThompson  
> Created at: 2021-11-04 02:38:47 UTC  
> Updated at: 2024-01-24 21:31:21 UTC  
> Closed at: 2024-01-24 20:50:35 UTC  
> Url: https://github.com/boostorg/math/pull/717  

…DEBUG

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-11-04 02:42:27 UTC  
> Url: https://github.com/boostorg/math/pull/717#issuecomment-960397595  

@jzmaddock : This prints out all the information available in tsv format. This PR is designed so you can just stuff it into scatterplot.online and get something like this:  
  
<img width="623" alt="Screen Shot 2021-11-03 at 7 40 33 PM" src="https://user-images.githubusercontent.com/5459618/140248728-820700f8-ff7d-456c-9a98-21593328599e.png">  
  
  
Looks faster than exponential to me. Maybe if something is going wrong, we also need to keep track of the number of function calls? Or perhaps use a different function.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-11-05 16:40:02 UTC  
> Url: https://github.com/boostorg/math/pull/717#issuecomment-962046557  

Thanks Nick, it's definitely converging as it should, taking cos(log(x)) over 0,1 == 0.5 as an example, and at 500 digit precision I see:  
  
<TABLE FRAME=VOID CELLSPACING=0 COLS=3 RULES=NONE BORDER=0>  
	<COLGROUP><COL WIDTH=86><COL WIDTH=86><COL WIDTH=86></COLGROUP>  
	<TBODY>  
		<TR>  
			<TD WIDTH=86 HEIGHT=17 ALIGN=LEFT>level</TD>  
			<TD WIDTH=86 ALIGN=LEFT>#calls</TD>  
			<TD WIDTH=86 ALIGN=LEFT>Found Error</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="2" SDNUM="2057;">2</TD>  
			<TD ALIGN=RIGHT SDVAL="55" SDNUM="2057;">55</TD>  
			<TD ALIGN=RIGHT SDVAL="0.000929" SDNUM="2057;">0.000929</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="3" SDNUM="2057;">3</TD>  
			<TD ALIGN=RIGHT SDVAL="109" SDNUM="2057;">109</TD>  
			<TD ALIGN=RIGHT SDVAL="0.0000000171" SDNUM="2057;0;0.00E+000">1.71E-008</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="4" SDNUM="2057;">4</TD>  
			<TD ALIGN=RIGHT SDVAL="218" SDNUM="2057;">218</TD>  
			<TD ALIGN=RIGHT SDVAL="1.9E-016" SDNUM="2057;0;0.00E+000">1.90E-016</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="5" SDNUM="2057;">5</TD>  
			<TD ALIGN=RIGHT SDVAL="436" SDNUM="2057;">436</TD>  
			<TD ALIGN=RIGHT SDVAL="1.91E-033" SDNUM="2057;0;0.00E+000">1.91E-033</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="6" SDNUM="2057;">6</TD>  
			<TD ALIGN=RIGHT SDVAL="873" SDNUM="2057;">873</TD>  
			<TD ALIGN=RIGHT SDVAL="6.17E-068" SDNUM="2057;0;0.00E+000">6.17E-068</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="7" SDNUM="2057;">7</TD>  
			<TD ALIGN=RIGHT SDVAL="1745" SDNUM="2057;">1745</TD>  
			<TD ALIGN=RIGHT SDVAL="1.21E-136" SDNUM="2057;0;0.00E+000">1.21E-136</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="8" SDNUM="2057;">8</TD>  
			<TD ALIGN=RIGHT SDVAL="3490" SDNUM="2057;">3490</TD>  
			<TD ALIGN=RIGHT SDVAL="2.96E-273" SDNUM="2057;0;0.00E+000">2.96E-273</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="9" SDNUM="2057;">9</TD>  
			<TD ALIGN=RIGHT SDVAL="6981" SDNUM="2057;">6981</TD>  
			<TD ALIGN=RIGHT SDVAL="0" SDNUM="2057;">0</TD>  
		</TR>  
	</TBODY>  
</TABLE>  
  
Which all looks good, but then for qthsh I get:  
  
<TABLE FRAME=VOID CELLSPACING=0 COLS=3 RULES=NONE BORDER=0>  
	<COLGROUP><COL WIDTH=86><COL WIDTH=86><COL WIDTH=86></COLGROUP>  
	<TBODY>  
		<TR>  
			<TD WIDTH=86 HEIGHT=17 ALIGN=LEFT>level</TD>  
			<TD WIDTH=86 ALIGN=LEFT>#calls</TD>  
			<TD WIDTH=86 ALIGN=LEFT>Found Error</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;">0</TD>  
			<TD ALIGN=RIGHT SDVAL="15" SDNUM="2057;">15</TD>  
			<TD ALIGN=RIGHT SDVAL="0.112" SDNUM="2057;">0.112</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="29" SDNUM="2057;">29</TD>  
			<TD ALIGN=RIGHT SDVAL="0.00048" SDNUM="2057;">0.00048</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="2" SDNUM="2057;">2</TD>  
			<TD ALIGN=RIGHT SDVAL="57" SDNUM="2057;">57</TD>  
			<TD ALIGN=RIGHT SDVAL="0.0000000514" SDNUM="2057;0;0.00E+000">5.14E-008</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="3" SDNUM="2057;">3</TD>  
			<TD ALIGN=RIGHT SDVAL="109" SDNUM="2057;">109</TD>  
			<TD ALIGN=RIGHT SDVAL="1.72E-016" SDNUM="2057;0;0.00E+000">1.72E-016</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="4" SDNUM="2057;">4</TD>  
			<TD ALIGN=RIGHT SDVAL="213" SDNUM="2057;">213</TD>  
			<TD ALIGN=RIGHT SDVAL="3.24E-034" SDNUM="2057;0;0.00E+000">3.24E-034</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="5" SDNUM="2057;">5</TD>  
			<TD ALIGN=RIGHT SDVAL="419" SDNUM="2057;">419</TD>  
			<TD ALIGN=RIGHT SDVAL="9.41E-068" SDNUM="2057;0;0.00E+000">9.41E-068</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="6" SDNUM="2057;">6</TD>  
			<TD ALIGN=RIGHT SDVAL="829" SDNUM="2057;">829</TD>  
			<TD ALIGN=RIGHT SDVAL="3.75E-136" SDNUM="2057;0;0.00E+000">3.75E-136</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="7" SDNUM="2057;">7</TD>  
			<TD ALIGN=RIGHT SDVAL="1645" SDNUM="2057;">1645</TD>  
			<TD ALIGN=RIGHT SDVAL="1.73E-256" SDNUM="2057;0;0.00E+000">1.73E-256</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="8" SDNUM="2057;">8</TD>  
			<TD ALIGN=RIGHT SDVAL="3275" SDNUM="2057;">3275</TD>  
			<TD ALIGN=RIGHT SDVAL="1.05E-253" SDNUM="2057;0;0.00E+000">1.05E-253</TD>  
		</TR>  
	</TBODY>  
</TABLE>  
</BODY>  
  
So for any given number of calls, the error is twice as good except right at the end.  
  
Then if we line up the abscissa values for the rows that gave 10^-8 error for each we see:  
  
<TABLE FRAME=VOID CELLSPACING=0 COLS=2 RULES=NONE BORDER=0>  
	<COLGROUP><COL WIDTH=86><COL WIDTH=86></COLGROUP>  
	<TBODY>  
		<TR>  
			<TD WIDTH=86 HEIGHT=18 ALIGN=LEFT>Boost</TD>  
			<TD WIDTH=86 ALIGN=LEFT>qthsh</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
			<TD ALIGN=RIGHT SDVAL="1" SDNUM="2057;">1</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.994" SDNUM="2057;">0.994</TD>  
			<TD ALIGN=RIGHT SDVAL="0.996" SDNUM="2057;">0.996</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.93" SDNUM="2057;">0.93</TD>  
			<TD ALIGN=RIGHT SDVAL="0.961" SDNUM="2057;">0.961</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.689" SDNUM="2057;">0.689</TD>  
			<TD ALIGN=RIGHT SDVAL="0.838" SDNUM="2057;">0.838</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.311" SDNUM="2057;">0.311</TD>  
			<TD ALIGN=RIGHT SDVAL="0.624" SDNUM="2057;">0.624</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.0702" SDNUM="2057;">0.0702</TD>  
			<TD ALIGN=RIGHT SDVAL="0.376" SDNUM="2057;">0.376</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.00648" SDNUM="2057;">0.00648</TD>  
			<TD ALIGN=RIGHT SDVAL="0.162" SDNUM="2057;">0.162</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.000156" SDNUM="2057;">0.000156</TD>  
			<TD ALIGN=RIGHT SDVAL="0.039" SDNUM="2057;">0.039</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.000000398" SDNUM="2057;0;0.00E+000">3.98E-007</TD>  
			<TD ALIGN=RIGHT SDVAL="0.00376" SDNUM="2057;">0.00376</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0.0000000000236" SDNUM="2057;0;0.00E+000">2.36E-011</TD>  
			<TD ALIGN=RIGHT SDVAL="0.0000842" SDNUM="2057;0;0.00E+000">8.42E-005</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="2.71E-018" SDNUM="2057;0;0.00E+000">2.71E-018</TD>  
			<TD ALIGN=RIGHT SDVAL="0.000000171" SDNUM="2057;0;0.00E+000">1.71E-007</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1.02E-029" SDNUM="2057;0;0.00E+000">1.02E-029</TD>  
			<TD ALIGN=RIGHT SDVAL="0.00000000000655" SDNUM="2057;0;0.00E+000">6.55E-012</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1.53E-048" SDNUM="2057;0;0.00E+000">1.53E-048</TD>  
			<TD ALIGN=RIGHT SDVAL="3.5E-019" SDNUM="2057;0;0.00E+000">3.50E-019</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1.43E-079" SDNUM="2057;0;0.00E+000">1.43E-079</TD>  
			<TD ALIGN=RIGHT SDVAL="3.63E-031" SDNUM="2057;0;0.00E+000">3.63E-031</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="1E-130" SDNUM="2057;0;0.00E+000">1.00E-130</TD>  
			<TD ALIGN=RIGHT SDVAL="6.4E-051" SDNUM="2057;0;0.00E+000">6.40E-051</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="4.62E-215" SDNUM="2057;0;0.00E+000">4.62E-215</TD>  
			<TD ALIGN=RIGHT SDVAL="1.74E-083" SDNUM="2057;0;0.00E+000">1.74E-083</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=RIGHT SDVAL="3.55E-137" SDNUM="2057;0;0.00E+000">3.55E-137</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=RIGHT SDVAL="1.07E-225" SDNUM="2057;0;0.00E+000">1.07E-225</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
		<TR>  
			<TD HEIGHT=17 ALIGN=RIGHT SDVAL="0" SDNUM="2057;0;@">0</TD>  
			<TD ALIGN=LEFT><BR></TD>  
		</TR>  
	</TBODY>  
</TABLE>  
</BODY>  
  
So the difference would seem to be all out in the tails, and how far out we choose to go.  Presumably then, tail pruning based on the values returned, should be the way to go, but we'll see I guess.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-11-05 17:31:26 UTC  
> Url: https://github.com/boostorg/math/pull/717#issuecomment-962083592  

@jzmaddock : How are you counting the calls? Should we add a counter?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-11-05 18:25:48 UTC  
> Url: https://github.com/boostorg/math/pull/717#issuecomment-962120037  

> How are you counting the calls? Should we add a counter?  
  
Easy: just instrument the function being integrated.

---
