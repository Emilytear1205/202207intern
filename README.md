# 202207intern
2022年暑期實務實習

### f70 檔案讀取並輸出(尚未設定揀選標準)

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

### f70 檔案讀取並輸出，定位品質閥值設定

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
