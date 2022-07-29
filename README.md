# 202207intern
2022年暑期實務實習

 <p align="right">
    指導者：蒲新杰研究員
    </p>

 <p align="right">
    作者：許芯慈
    </p>

 <p align="right">
    最後更新時間：2022/07/28
    </p>
  

### GMT 5 作圖
+ 不管現在是想要做甚麼圖出來，要好好仔細睜大眼睛看"JM"還有"JX"有沒有"底" "圖"配合
+ "wysiwyg" 是"GMT color table"中的一組color bar顏色系列。
+ -T-100/0/10 可調整color bar的數值以及間距。
+ -D9/0/15/0.6h，代表x軸=9inchs，y軸=0incds，大小為15*0.6平方英吋。h代表color bar是水平的。
+ 在做陰影的時候可以利用"-A"去調整太陽的照射角度
+ 在"規模以球體大小區分"這塊，要注意的事情是，-Sc的部分後面不要限定大小，並且在$4的後面要加上"/ 某數字"，才可以得到有小漸大的球體。
+ coast--海岸線，g--ground，S--sea，後面接的是預設顏色。
+ -B2af0.5g1，邊框，每2度標示一次座標，刻度(黑 白線區間)。
+ 每0.5度標示一次(黑白區間/比例尺為0.5)，格線每1度就有一條。
+ 此處的"度"為經度或是緯度
+ -JM15c，代表橫向寬幅為15公分(6i就是6英吋)。
+ -Df，-D可用來控制資料解析度，-Df屬於最高等級的解析度。
+ -A0.1，可省略比0.1平方公里還要小的物體(陸地、小島)。
+ -P的功用在於可將原本預設為橫向的A4紙張，改成直向的。
+ psconvert的指令可參考此連結：https://docs.gmt-china.org/6.1/module/psconvert/


### Taiwan2.ps 利用不同角度和剖面去分析2013年3月和6月的南投地震之地震序列
+ 顏色隨深度變化
```

###我不管現在是想要做甚麼圖出來，一定要好好仔細睜大眼睛看JM還有JX有沒有"底" "圖"配合###

###colorbar
gmtset COLOR_MODEL +rgb
###地震深度的變化顏色
gmt makecpt -Cwysiwyg -T-0/60/10 -Z > base1.cpt

###底圖($1經度；$2緯度；$3深度；$4規模；年分，深度以顏色區分，規模以球體大小區分)
psbasemap -R119.5/122.5/22.5/25 -JM10c -Ba1f0.5g1 -L123.1/21.9/0/50 -BWSne+t"Earthquake Distribution" -K -Y18 -P> Taiwan2.ps
###海岸線(畫出台灣本島)
pscoast -R -JM -W0.1p,black -Gdarkseagreen2 -Scornflowerblue -Dh -Na -O -K -P >> Taiwan2.ps


###剖面圖的起點與終點，離剖線的最短距離是$13
echo 120.8 24.1 > 1.tmp
echo 121.4 23.7 >> 1.tmp
psxy -R -JM -O -K -W2 1.tmp >> Taiwan2.ps
echo 120.7 24 >2.tmp
echo 121.3 23.6 >> 2.tmp
psxy -R -JM -O -K -W2 2.tmp >> Taiwan2.ps
project -C120.8/24.1 -E121.4/23.7 -Qpqrs .\data\2013_100km.txt >tmp2_1

###測站
gawk "{print $2,$3}" .\data\nsta24.dat | psxy -JM -R -K -O -St0.15 -W0.5 >> Taiwan2.ps

###地震點圖($1經度；$2緯度；$3深度；$4規模；年分，深度以顏色區分，規模以球體大小區分)
###算出距離並且進行擇選
rem gawk "($4<=3)&&($13<=10)&&($13>=-10) {print $1,$2,$3,$4/20}" tmp2_1 | psxy -R -JM  -K -O -SC -W0.1,black -P -V >> Taiwan2.ps
gawk "($4<=4)&&($12<100)&&($11<=50)&&($11>=-50) {print $1,$2,$3,$4/15}" tmp2_1 | psxy -R -JM  -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
gawk "($4>4)&&($12<100)&&($11<=50)&&($11>=-50) {print $1,$2,$3,$4/15}" tmp2_1 | psxy -R -JM  -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
rem gawk "($4>6) {print $1,$2}" .\data\0_100_year.txt| psxy -R -JM -K -O -Sa0.7 -W0.01 -G255/0/0 >> Taiwan2.ps

###地震震央
echo 121.05 23.9 | gmt psxy -R -J -Sa0.7 -W0.01 -Gred -O -K >> Taiwan2.ps
echo 120.97 23.86 | gmt psxy -R -J -Sa0.7 -W0.01 -Gyellow -O -K >> Taiwan2.ps

###3月機制球
echo 121.05 23.9 19.4 317 19 31 6.2 122 24 2013/03/27 > beach_ball.tmp
psmeca -R -JM -Sa0.5c/u -C -Gred -K -O beach_ball.tmp >> Taiwan2.ps
###6月機制球
echo 120.97 23.86 14.5 2 22 83 6.5 122 23 2013/06/02 > beach_ball2.tmp
psmeca -R -JM -Sa0.5c/u -C -Gyellow -K -O beach_ball2.tmp >> Taiwan2.ps

###畫出斷層線
psxy .\data\2021fault97.txt -JM -R -K -O -W2,255/0/0 -P >> Taiwan2.ps



###經度剖面(bottom)
psbasemap -R119.5/122.5/-10/100 -Jx3.45/-0.03 -Bxa1f1+u"@+O@+" -Bya100f20 -BWSne -X-0.2 -Y-5.4 -O -K >> Taiwan2.ps
gawk "($4<=4)&&($13<=10)&&($13>=-10) {print $1,$3,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
gawk "($4>4)&&($13<=10)&&($13>=-10) {print $1,$3,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
echo 121.05 19.4 | psxy -R -Jx -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan2.ps
###緯度剖面(right)
psbasemap -R-10/100/22.5/25 -Jx0.038/3.8 -Bxa100f20 -Bya1f1+u"@+O@+" -BESwn -X10.6 -Y5.2 -O -K>> Taiwan2.ps
gawk "($4<=4)&&($13<=10)&&($13>=-10) {print $3,$2,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
gawk "($4>44)&&($13<=10)&&($13>=-10) {print $3,$2,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
echo 19.4 23.9 | psxy -R -Jx -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan2.ps

###製作剖面圖
psbasemap -R0/160/0/60 -JX10.5/-4 -Bxa40f20 -Bya40f20 -BWSne -Y-13c -X-10.6 -O -K >> Taiwan2.ps
gawk "($12<100)&&($13<=10)&&($13>=-10) {print $12,$3,$3,$4/15}" tmp2_1 | psxy -R -JX -K -O -SC -Cbase1.cpt -W0.1 >> Taiwan2.ps
gawk "($4>6)&&($6<6) {print $12,$3,$3}" tmp2_1 | psxy -R -JX -K -O -Sa0.7 -W0.01 -G255/0/0 >> Taiwan2.ps
###機制球
pscoupe -JX -R -Sa0.5/u -Gred -Aa120.8/24.1/121.4/23.7/90/20/0/40 -K -O -Q -L -N beach_ball.tmp >> Taiwan2.ps
pscoupe -JX -R -Sa0.5/u -Gyellow -Aa120.7/24/121.3/23.6/90/20/0/40 -K -O -Q -L -N beach_ball2.tmp >> Taiwan2.ps


###顯示colorbar
gmt psscale -Cbase1.cpt -D14.4/9.7/7/0.6h -B+l"Depth(km)" -K -O >>Taiwan2.ps
rem -D13.9/10/5/0.6h

###顯示規模大小的圖例
echo 1.0 2 2 > tmp.m
echo 1.5 2 3 >> tmp.m
echo 2.0 2 4 >> tmp.m
echo 2.5 2 5 >> tmp.m
echo 3.0 2 6 >> tmp.m
echo 3.5 2 7 >> tmp.m
rem #下面這行是畫出不同比例圓球的
gawk "{print $1,$2,$3/15}" tmp.m | gmt psxy -R0/8/0/10 -JX -Sc -W0.1 -K -O -X11 -Y-0.1>> Taiwan2.ps
pstext tmp.m -R -JX -F+f8p -D0/0.8 -K -O -X0 -Y0.2 -N >> Taiwan2.ps
echo 0 0 Magnitude(M@-L@-): | gmt pstext -R -JX -F+f10p,1+jML -N -O -X0.6 -Y1.3 >> Taiwan2.ps


###轉換格式(轉成jpg)
rem gmt psconvert -A -Tj -FNantou_2013 Taiwan2.ps

del *.tmp tmp*
del *.cpt*

# -D14.3/10/6/0.6 呈現出來的是直的，-D14.3/10/6/0.6h是水平的

```

### Taiwan3.ps 利用不同角度和剖面去分析2013年3月和6月的南投地震之地震序列
### 2013_compare.ps 是將剖面和震源機制解單獨繪製出來討論
+ 顏色隨月份變化
```

###我不管你現在是想要做甚麼圖出來，拜託請給我好好仔細睜大眼睛看你的JM還有JX有沒有"底" "圖"配合###

###colorbar
rem gmtset COLOR_MODEL +rgb
gmtset COLOR_MODEL 
###地震深度的變化顏色
makecpt -Csealand -T1/12/1> base1.cpt

###底圖($1經度；$2緯度；$3深度；$4規模；年分，深度以顏色區分，規模以球體大小區分)
psbasemap -R119.5/122.5/22.5/25 -JM10c -Ba1f0.5g1 -L123.1/21.9/0/50 -BWSne+t"Earthquake Distribution" -K -Y18 -P> Taiwan3.ps
###海岸線(畫出台灣本島)
pscoast -R -JM -W0.1p,black -Gdarkseagreen2 -Scornflowerblue -Dh -Na -O -K -P >> Taiwan3.ps


###剖面圖的起點與終點，離剖線的最短距離是$13
rem 3月
echo 120.8 24.1 > 1.tmp
echo 121.4 23.7 >> 1.tmp
psxy -R -JM -O -K -W2 1.tmp >> Taiwan3.ps
rem 6月
echo 120.7 24 >2.tmp
echo 121.3 23.6 >> 2.tmp
psxy -R -JM -O -K -W2 2.tmp >> Taiwan3.ps
project -C120.8/24.1 -E121.4/23.7 -Qpqrs .\data\2013_100km.txt >tmp2_1

###測站
gawk "{print $2,$3}" .\data\nsta24.dat | psxy -JM -R -K -O -St0.15 -W0.5 >> Taiwan3.ps

###地震點圖($1經度；$2緯度；$3深度；$4規模；年分，深度以顏色區分，規模以球體大小區分)
###算出距離並且進行擇選
gawk "($4<=4)&&($12<100)&&($11<=50)&&($11>=-50) {print $1,$2,$3,$4/15}" tmp2_1 | psxy -R -JM  -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan3.ps
gawk "($4>4)&&($12<100)&&($11<=50)&&($11>=-50) {print $1,$2,$3,$4/15}" tmp2_1 | psxy -R -JM  -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan3.ps

###地震震央
echo 121.05 23.9 | gmt psxy -R -J -Sa0.7 -W0.01 -Gred -O -K >> Taiwan3.ps
echo 120.97 23.86 | gmt psxy -R -J -Sa0.7 -W0.01 -Gyellow -O -K >> Taiwan3.ps

###3月機制球
echo 121.05 23.9 19.4 317 19 31 6.2 122 24 2013/03/27 > beach_ball.tmp
psmeca -R -JM -Sa0.5c/u -C -Gred -K -O beach_ball.tmp >> Taiwan3.ps
###6月機制球
echo 120.97 23.86 14.5 2 22 83 6.5 122 23 2013/06/02 > beach_ball2.tmp
psmeca -R -JM -Sa0.5c/u -C -Gyellow -K -O beach_ball2.tmp >> Taiwan3.ps

###畫出斷層線
psxy .\data\2021fault97.txt -JM -R -K -O -W2,255/0/0 -P >> Taiwan3.ps

###經度剖面(bottom)
psbasemap -R119.5/122.5/-10/100 -Jx3.45/-0.03 -Bxa1f1+u"@+O@+" -Bya100f20 -BWSne -X-0.2 -Y-5.4 -O -K >> Taiwan3.ps
gawk "($4<=4)&&($13<=10)&&($13>=-10) {print $1,$3,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan3.ps
gawk "($4>4)&&($13<=10)&&($13>=-10) {print $1,$3,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan3.ps
echo 121.05 19.4 | psxy -R -Jx -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan3.ps
###緯度剖面(right)
psbasemap -R-10/100/22.5/25 -Jx0.038/3.8 -Bxa100f20 -Bya1f1+u"@+O@+" -BESwn -X10.6 -Y5.2 -O -K>> Taiwan3.ps
gawk "($4<=4)&&($13<=10)&&($13>=-10) {print $3,$2,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan3.ps
gawk "($4>44)&&($13<=10)&&($13>=-10) {print $3,$2,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan3.ps
echo 19.4 23.9 | psxy -R -Jx -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan3.ps

###製作剖面圖
psbasemap -R0/160/0/60 -JX10.5/-4 -Bxa40f20 -Bya40f20 -BWSne -Y-13c -X-10.6 -O -K >> Taiwan3.ps
gawk "($12<100)&&($13<=10)&&($13>=-10) {print $12,$3,$3,$4/15}" tmp2_1 | psxy -R -JX -K -O -SC -Cbase1.cpt -W0.1 >> Taiwan3.ps
gawk "($4>6)&&($6<6) {print $12,$3,$3}" tmp2_1 | psxy -R -JX -K -O -Sa1.0 -W0.01 -G255/0/0 >> Taiwan3.ps
gawk "($4>6)&&($6>=6) {print $12,$3,$3}" tmp2_1 | psxy -R -JX -K -O -Sa1.0 -W0.01 -Gyellow >> Taiwan3.ps
###機制球
pscoupe -JX -R -Sa0.5/u -Gred  -Aa120.8/24.1/121.4/23.7/90/20/0/40 -K -O -Q -L -N beach_ball.tmp >> Taiwan3.ps
pscoupe -JX -R -Sa0.5/u -Gyellow -Aa120.7/24/121.3/23.6/90/20/0/40 -K -O -Q -L -N beach_ball2.tmp >> Taiwan3.ps


###顯示colorbar
gmt psscale -Cbase1.cpt -D14.4/9.7/7/0.6h -B+l"2013_Month" -K -O >>Taiwan3.ps

###顯示規模大小的圖例
echo 1.0 2 2 > tmp.m
echo 1.5 2 3 >> tmp.m
echo 2.0 2 4 >> tmp.m
echo 2.5 2 5 >> tmp.m
echo 3.0 2 6 >> tmp.m
echo 3.5 2 7 >> tmp.m
rem #下面這行是畫出不同比例圓球的
gawk "{print $1,$2,$3/15}" tmp.m | gmt psxy -R0/8/0/10 -JX -Sc -W0.1 -K -O -X11 -Y-0.1>> Taiwan3.ps
pstext tmp.m -R -JX -F+f8p -D0/0.8 -K -O -X0 -Y0.2 -N >> Taiwan3.ps
echo 0 0 Magnitude(M@-L@-): | gmt pstext -R -JX -F+f10p,1+jML -N -O -X0.6 -Y1.3 >> Taiwan3.ps


###單獨畫出來
##3月份
psbasemap -R0/80/0/60 -JX12/-9 -Bxa10f5 -Bya20f10+l"Before May" -BWSne -K -P -X4 -Y20 > 2013_compare.ps
gawk "($6<=5)&&($4<=4)&&($12<50)&&($13<=10)&&($13>=-10) {print $12,$3,$6,$4/15}" tmp2_1 | psxy -R -JX -K -O -SC -Cbase1.cpt -W0.1 >>2013_compare.ps
gawk "($6<=5)&&($4>4)&&($12<50)&&($13<=10)&&($13>=-10) {print $12,$3,$6,$4/15}" tmp2_1 | psxy -R -JX -K -O -SC -Cbase1.cpt -W0.1 >>2013_compare.ps
gawk "($4>6)&&($6<6) {print $12,$3,$3}" tmp2_1 | psxy -R -JX -K -O -Sa1.0 -W0.01 -Gred >> 2013_compare.ps
pscoupe -JX -R -Sa0.5/u -Gred -Aa120.8/24.1/121.4/23.7/90/20/0/40 -K -O -Q -L -N beach_ball.tmp >> 2013_compare.ps
rem pscoupe -JX -R -Sa0.5/u -Gyellow -Aa120.7/24/121.3/23.6/90/20/0/40 -K -Q -L -N beach_ball2.tmp>> 2013_compare.ps
##6月份
psbasemap -R0/80/0/60 -JX12/-9 -Bxa10f5 -Bya20f10+l"After June" -BWSne -K -O -X0 -Y-12 >> 2013_compare.ps
gawk "($6>5)&&($4<=4)&&($12<50)&&($13<=10)&&($13>=-10) {print $12,$3,$6,$4/15}" tmp2_1 | psxy -R -JX -K -O -SC -Cbase1.cpt -W0.1 >>2013_compare.ps
gawk "($6>5)&&($4>4)&&($12<50)&&($13<=10)&&($13>=-10) {print $12,$3,$6,$4/15}" tmp2_1 | psxy -R -JX -K -O -SC -Cbase1.cpt -W0.1 >>2013_compare.ps
gawk "($4>6)&&($6>=6) {print $12,$3,$3}" tmp2_1 | psxy -R -JX -K -O -Sa1.0 -W0.01 -Gyellow >> 2013_compare.ps
pscoupe -JX -R -Sa0.5/u -Gyellow -Aa120.7/24/121.3/23.6/90/20/0/40 -K -O -Q -L -N beach_ball2.tmp >> 2013_compare.ps

###顯示colorbar
gmt psscale -Cbase1.cpt -D14/11/7/0.6 -By+l"2013_Month" -K -O >> 2013_compare.ps

###轉換格式(轉成jpg)
rem gmt psconvert -A -Tj -FNantou_Month_compare Taiwan3.ps

del *.tmp tmp*
del *.cpt*

# -D14.3/10/6/0.6 呈現出來的是直的，-D14.3/10/6/0.6h是水平的

```

### Taiwab2.ps，利用不同角度和剖面去分析2018年花蓮地震的地震序列

```

###我不管現在是想要做甚麼圖出來，一定要好好仔細睜大眼睛看JM還有JX有沒有"底" "圖"配合###

###colorbar
gmtset COLOR_MODEL +rgb
###地震深度的變化顏色
gmt makecpt -Cwysiwyg -T-0/60/10 -Z > base1.cpt

###底圖($1經度；$2緯度；$3深度；$4規模；年分，深度以顏色區分，規模以球體大小區分)
psbasemap -R120.5/123.5/23/25.5 -JM10c -Ba1f0.5g1 -L124.1/22.4/0/50 -BWSne+t"Earthquake Distribution" -K -Y18 -P> Taiwan2.ps
###海岸線(畫出台灣本島)
pscoast -R -JM -W0.1p,black -Gdarkseagreen2 -Scornflowerblue -Df -Na -O -K -P >> Taiwan2.ps


### 剖面圖的起點與終點，離剖線的最短距離是$13
echo 121 24.9 > 1.tmp
echo 122.5 23.3 >> 1.tmp
psxy -R -JM -O -K -W2 1.tmp >> Taiwan2.ps
project -C121/24.9 -E122.5/23.3 -Qpqrs .\data\0_100_year.txt >tmp2_1

###地震點圖($1經度；$2緯度；$3深度；$4規模；年分，深度以顏色區分，規模以球體大小區分)
###算出距離並且進行擇選
gawk "($13<=10)&&($13>=-10) {print $1,$2,$3,$4}" tmp2_1 | psxy -R -JM  -K -O -SC0.2 -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
rem gawk "($4>6) {print $1,$2}" .\data\0_100_year.txt| psxy -R -JM -K -O -Sa0.7 -W0.01 -G255/0/0 >> Taiwan2.ps
###畫出斷層線
psxy .\data\2021fault97.txt -JM -R -K -O -W2,255/0/0 -P >> Taiwan2.ps

###2018花蓮地震震央位置
echo 121.73 24.10 | gmt psxy -R -J -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan2.ps

###經度剖面(bottom)
psbasemap -R120.5/123.5/-10/100 -Jx3.45/-0.03 -Bxa1f1+u"@+O@+" -Bya100f20 -BWSne -X-0.2 -Y-5.4 -O -K >> Taiwan2.ps
gawk "($13<=10)&&($13>=-10) {print $1,$3,$3}" tmp2_1 | psxy -R -Jx -K -O -SC0.2 -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
echo 121.73 6.3 | psxy -R -Jx -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan2.ps
###緯度剖面(right)
psbasemap -R-10/100/23/25.5 -Jx0.038/3.8 -Bxa100f20 -Bya1f1+u"@+O@+" -BESwn -X10.6 -Y5.2 -O -K>> Taiwan2.ps
gawk "($13<=10)&&($13>=-10) {print $3,$2,$3}" tmp2_1 | psxy -R -Jx -K -O -SC0.2 -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
echo 6.3 24.10 | psxy -R -Jx -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan2.ps

###製作剖面圖
psbasemap -R0/200/0/80 -JX10.5/-5.7 -Bxa40f20 -Bya40f20 -BWSne -Y-13c -X-10.5 -O -K >> Taiwan2.ps
gawk "($13<=10)&&($13>=-10) {print $12,$3,$3}" tmp2_1 | psxy -R -JX -K -O -SC0.2 -Cbase1.cpt -W0.1 >> Taiwan2.ps
gawk "($4>6) {print $12,$3,$3}" tmp2_1 | psxy -R -JX -K -O -Sa0.7 -W0.01 -G255/0/0 >> Taiwan2.ps

###顯示colorbar
gmt psscale -Cbase1.cpt -D13.9/10/5/0.6h -Bx+t"Depth_km" -O >>Taiwan2.ps

###轉換格式
gmt psconvert -A -Tj -FHualien_2018 Taiwan2.ps

del *.tmp tmp*

```

### 顏色是照著震源深度

```
###我不管你現在是想要做甚麼圖出來，拜託請給我好好仔細睜大眼睛看你的JM還有JX有沒有"底" "圖"配合###

###colorbar
gmtset COLOR_MODEL +rgb
###地震深度的變化顏色
gmt makecpt -Cwysiwyg -T-0/60/10 -Z > base1.cpt
###地震發生時間的變化顏色
gmt makecpt -Cterra -T-0/60/10 -Z > terra.cpt

###底圖($1經度；$2緯度；$3深度；$4規模；年分，深度以顏色區分，規模以球體大小區分)
psbasemap -R119.5/122.5/22.5/25 -JM10c -Ba1f0.5g1 -L123.1/21.9/0/50 -BWSne+t"Earthquake Distribution" -K -Y18 -P> Taiwan2.ps
###海岸線(畫出台灣本島)
pscoast -R -JM -W0.1p,black -Gdarkseagreen2 -Scornflowerblue -Dh -Na -O -K -P >> Taiwan2.ps


###剖面圖的起點與終點，離剖線的最短距離是$13
echo 120.8 24.1 > 1.tmp
echo 121.4 23.7 >> 1.tmp
psxy -R -JM -O -K -W2 1.tmp >> Taiwan2.ps
echo 120.7 24 >2.tmp
echo 121.3 23.6 >> 2.tmp
psxy -R -JM -O -K -W2 2.tmp >> Taiwan2.ps
project -C120.8/24.1 -E121.4/23.7 -Qpqrs .\data\2013_100km.txt >tmp2_1

###測站
gawk "{print $2,$3}" .\data\nsta24.dat | psxy -JM -R -K -O -St0.15 -W0.5 >> Taiwan2.ps

###地震點圖($1經度；$2緯度；$3深度；$4規模；年分，深度以顏色區分，規模以球體大小區分)
###算出距離並且進行擇選
rem gawk "($4<=3)&&($13<=10)&&($13>=-10) {print $1,$2,$3,$4/20}" tmp2_1 | psxy -R -JM  -K -O -SC -W0.1,black -P -V >> Taiwan2.ps
gawk "($4<=4)&&($12<100)&&($11<=50)&&($11>=-50) {print $1,$2,$3,$4/15}" tmp2_1 | psxy -R -JM  -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
gawk "($4>4)&&($12<100)&&($11<=50)&&($11>=-50) {print $1,$2,$3,$4/15}" tmp2_1 | psxy -R -JM  -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
rem gawk "($4>6) {print $1,$2}" .\data\0_100_year.txt| psxy -R -JM -K -O -Sa0.7 -W0.01 -G255/0/0 >> Taiwan2.ps
###3月機制球
echo 121.05 23.9 19.4 317 19 31 6.2 122 24 2013/03/27 > beach_ball.tmp
psmeca -R -JM -Sa0.5c/u -C -Gred -K -O beach_ball.tmp >> Taiwan2.ps
###6月機制球
echo 120.97 23.86 14.5 2 22 83 6.5 122 23 2013/06/02 > beach_ball2.tmp
psmeca -R -JM -Sa0.5c/u -C -Gyellow -K -O beach_ball2.tmp >> Taiwan2.ps

###畫出斷層線
psxy .\data\2021fault97.txt -JM -R -K -O -W2,255/0/0 -P >> Taiwan2.ps

###地震震央
echo 121.05 23.9 | gmt psxy -R -J -Sa0.7 -W0.01 -Gred -O -K >> Taiwan2.ps
echo 120.97 23.86 | gmt psxy -R -J -Sa0.7 -W0.01 -Gyellow -O -K >> Taiwan2.ps

###經度剖面(bottom)
psbasemap -R119.5/122.5/-10/100 -Jx3.45/-0.03 -Bxa1f1+u"@+O@+" -Bya100f20 -BWSne -X-0.2 -Y-5.4 -O -K >> Taiwan2.ps
gawk "($4<=4)&&($13<=10)&&($13>=-10) {print $1,$3,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
gawk "($4>4)&&($13<=10)&&($13>=-10) {print $1,$3,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
echo 121.05 19.4 | psxy -R -Jx -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan2.ps
###緯度剖面(right)
psbasemap -R-10/100/22/24.5 -Jx0.038/3.8 -Bxa100f20 -Bya1f1+u"@+O@+" -BESwn -X10.6 -Y5.2 -O -K>> Taiwan2.ps
gawk "($4<=4)&&($13<=10)&&($13>=-10) {print $3,$2,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
gawk "($4>44)&&($13<=10)&&($13>=-10) {print $3,$2,$3,$4/15}" tmp2_1 | psxy -R -Jx -K -O -SC -Cbase1.cpt -W0.1 -P -V >> Taiwan2.ps
echo 19.4 23.9 | psxy -R -Jx -Sa0.7 -W0.01 -G255/0/0 -O -K >> Taiwan2.ps

###製作剖面圖
psbasemap -R0/160/0/60 -JX10.5/-4 -Bxa40f20 -Bya40f20 -BWSne -Y-13c -X-10.6 -O -K >> Taiwan2.ps
gawk "($12<100)&&($13<=10)&&($13>=-10) {print $12,$3,$3,$4/15}" tmp2_1 | psxy -R -JX -K -O -SC -Cbase1.cpt -W0.1 >> Taiwan2.ps
gawk "($4>6)&&($7<6) {print $12,$3,$3}" tmp2_1 | psxy -R -JX -K -O -Sa0.7 -W0.01 -G255/0/0 >> Taiwan2.ps
###機制球
pscoupe -JX -R -Sa0.5/u -Gred -Aa120/24.0/121.5/23.5/90/20/0/40 -K -O -Q -L -N beach_ball.tmp >> Taiwan2.ps
pscoupe -JX -R -Sa0.5/u -Gyellow -Aa119.8/24.5/120.5/23.6/90/20/0/40 -K -O -Q -L -N beach_ball2.tmp >> Taiwan2.ps


###顯示colorbar
gmt psscale -Cbase1.cpt -D14.4/9.7/7/0.6h -B+l"Depth(km)" -K -O >>Taiwan2.ps
rem -D13.9/10/5/0.6h

###顯示規模大小的圖例
echo 1.0 2 2 > tmp.m
echo 1.5 2 3 >> tmp.m
echo 2.0 2 4 >> tmp.m
echo 2.5 2 5 >> tmp.m
echo 3.0 2 6 >> tmp.m
echo 3.5 2 7 >> tmp.m
rem #下面這行是畫出不同比例圓球的
gawk "{print $1,$2,$3/15}" tmp.m | gmt psxy -R0/8/0/10 -JX -Sc -W0.1 -K -O -X11 -Y-0.1>> Taiwan2.ps
pstext tmp.m -R -JX -F+f8p -D0/0.8 -K -O -X0 -Y0.2 -N >> Taiwan2.ps
echo 0 0 Magnitude(M@-L@-): | gmt pstext -R -JX -F+f10p,1+jML -N -O -X0.6 -Y1.3 >> Taiwan2.ps


###轉換格式(轉成jpg)
rem gmt psconvert -A -Tj -FNantou_2013 Taiwan2.ps

del *.tmp tmp*

# -D14.3/10/6/0.6 呈現出來的是直的，-D14.3/10/6/0.6h是水平的

```



### f70 檔案讀取並輸出(尚未設定揀選標準)
+ 如果一列程式碼過長，cmd會無法讀取，需要加入&(前面要空5格) 
+ 編碼從10開始，用4或5程式會壞掉
+ 如後第一列記得要空
+  REWIND現在仍然很常用。假如程序需要兩遍讀入同一個文本格式數據文件，讀完第一遍後，用REWIND回到文件頭部。          
+  BACKSPACE用於回退一個記錄。後面通常加上文件號，這裡的記錄可以理解為一個回車符號。

```


c	Program test
	implicit none
      character*100 Ain,Aout,Afi,Aqua
	real:: fs,fla,flon,fdep,fmag,fdmin,ftr,fH,fZ
	integer:: iy,im,id,ih,imin,ins,igap,Inph

	write(*,*)"input filename?"
	read(*,'(A100)') Ain
c	write會輸出文字，read的功能在於讓使用者輸入檔名，編譯器會進行讀取
	write(*,*)"output filename?"
	read(*,'(A100)') Aout

	open (10,file=Ain)
	open (11,file=Aout)
110	read(10,*,END=199)iy,im,id,ih,imin,fs,fla,flon,fdep,fmag,ins,fdmin
     &,igap,ftr,fH,fZ,Afi,Inph,Aqua
c	open和read，開啟檔案並進行讀取
	
c	if(flat.ge.?? .and. flat.le.?? .....) then

	write(11,132)iy,im,id,ih,imin,fs,fla,flon,fdep,fmag,ins,fdmin,igap
     &,ftr,fH,fZ,Afi,Inph,Aqua
132	format(i4,4(1x,i2),f5.2,1x,f7.4,1x,f8.4,1x,f6.2,1x,f4.2,1x,i3,1x,
     &f5.1,1x,i3,1x,f5.2,1x,f6.2,1x,f6.2,1x,A1,1x,I3,1x,A1)

	go to 110
199	close(10)
	close(11)
	stop
	end


```


### f70 檔案讀取並輸出，將定位品質從英文轉成數字

```
c	Program test
	implicit none
      character*100 Ain,Aout,Afi,Aqua
	real:: fs,fla,flon,fdep,fmag,fdmin,ftr,fH,fZ
	integer:: iy,im,id,ih,imin,ins,igap,Inph,q1

	write(*,*)"input filename?"
	read(*,'(A100)') Ain
c	write會輸出文字，read的功能在於讓使用者輸入檔名，編譯器會進行讀取
	write(*,*)"output filename?"
	read(*,'(A100)') Aout

	open (10,file=Ain)
	open (11,file=Aout)
110	read(10,*,END=199)iy,im,id,ih,imin,fs,fla,flon,fdep,fmag,ins,fdmin
     &,igap,ftr,fH,fZ,Afi,Inph,Aqua
c	open和read，開啟檔案並進行讀取

	if(Aqua.eq.'A') then
	q1=1
	else if (Aqua.eq.'B') then
	q1=2
	else if(Aqua.eq.'c') then
	q1=3
	else if(Aqua.eq.'B') then
	q1=4
	end if

	write(11,132)iy,im,id,ih,imin,fs,fla,flon,fdep,fmag,ins,fdmin,igap
     &,ftr,fH,fZ,Afi,Inph,Aqua
132	format(i4,4(1x,i2),f5.2,1x,f7.4,1x,f8.4,1x,f6.2,1x,f4.2,1x,i3,1x,
     &f5.1,1x,i3,1x,f5.2,1x,f6.2,1x,f6.2,1x,A1,1x,I3,1x,A1)

	go to 110
199	close(10)
	close(11)
	stop
	end

```
