# 202207intern
2022年暑期實務實習

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


### Taiwab2.ps，利用不同角度和剖面去分析2018年花蓮地震的地震序列

```

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

rem del *.tmp tmp*

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
