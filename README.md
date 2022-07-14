# 202207intern
2022年暑期實務實習

### f70 檔案讀取並輸出(尚未設定則選標準)

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
	
c	2000-01-14,08:14:33.50,24.9723,121.7732,122.08,3.96,33,11.00,89,0.30,1.60,1.10,F,58,A
c	if(flat.ge.?? .and. flat.le.?? .....) then

	write(11,132)iy,im,id,ih,imin,fs,fla,flon,fdep,fmag,ins,fdmin,igap
     &,ftr,fH,fZ,Afi,Inph,Aqua
132	format(i4,4(1x,i2),f5.2,1x,f7.4,1x,f8.4,1x,f6.2,1x,f4.2,1x,i3,1x,
     &f5.1,1x,i3,1x,f5.2,1x,f6.2,1x,f6.2,1x,A1,1x,I3,1x,A1)

c2000 01 01 01 42 18.36 23.7532 120.8820 7.92 3.10 30 14.50 56 0.20 0.30 0.40 F 56 B

	go to 110
199	close(10)
	close(11)
	stop
	end

```
