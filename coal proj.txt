TITLE My First Program (Test.asm)
INCLUDE Irvine32.inc
.model flat, stdcall
xsubxmean proto ,ptabley :ptr dword, pstock:ptr dword ,l :dword ,mean:dword
ysubymean proto
.data
stockprice dword 10,22,33,41,52,61,78,81,95,101
len =($-stockprice)/4
stockprice_mean dword 0
time dword 1,2,3,4,5,6,7,8,9,10
time_mean dword 0
table_ysubymean dword  len dup(?)
table_xsubxmean dword len dup(?)
table_ysubymeansq dword len dup(?)
table_xsubxmeansq dword len dup(?)
table_diffmeansq dword len dup(?)
sumofdiff dword 0
sumofxsq  dword 0
sumofysq  dword 0

 
.code
main PROC
;mean of stockprice y
mov ecx , lengthof stockprice
mov esi,offset stockprice
mov eax ,0
l1: add eax , [esi]
add esi,4
loop l1 ; eax have sum of all values
mov ebx , lengthof stockprice ;number of values
mov edx ,0 ;;emptying for divide
div ebx
mov stockprice_mean , eax

;mean of time x
mov ecx , lengthof time
mov esi,offset time
mov eax ,0
l2: add eax , [esi]
add esi,4
loop l2 ; eax have sum of all values of time
mov ebx , lengthof time ;number of values
mov edx ,0 ;;emptying for divide
div ebx
mov time_mean , eax

;table of (y-ymean)
mov edi ,offset table_ysubymean  
mov esi ,offset stockprice ;second parameter offset of stock price array
mov ecx, lengthof stockprice; second parameter number of elements in stockprice
mov ebx,stockprice_mean;fourth perimeter
start:
mov eax ,[esi]
mov [edi],eax
sub [edi],ebx
;mov eax , [edi]
;call writeint
add edi,type stockprice
add esi , type stockprice
loop start

;table of (x-xmean)
mov edi ,offset table_xsubxmean  
mov esi ,offset time ;second parameter offset of stock price array
mov ecx, lengthof time; second parameter number of elements in stockprice
mov ebx,time_mean;fourth perimeter
begin:
mov eax ,[esi]
mov [edi],eax
sub [edi],ebx
mov eax , [edi]
;call writeint
add edi,type time
add esi , type time
loop begin

;table of (y-ymean )squared
mov edi ,offset table_ysubymeansq
mov esi ,offset stockprice ;second parameter offset of stock price array
mov ecx, lengthof stockprice; second parameter number of elements in stockprice
mov ebx,stockprice_mean;fourth perimeter
looop:
mov eax ,[esi]
mov [edi],eax
sub [edi],ebx
mov eax , [edi]
mul eax 
mov [edi] ,eax
add sumofxsq , eax 
add edi,type stockprice
add esi , type stockprice
loop looop

;table of (x-xmean )squared
mov edi ,offset table_xsubxmeansq
mov esi ,offset time ;second parameter offset of stock price array
mov ecx, lengthof time; second parameter number of elements in stockprice
mov ebx,time_mean;fourth perimeter
next:
  mov eax ,[esi]
  mov [edi],eax
  sub [edi],ebx
  mov eax , [edi]
  mul eax 
  mov [edi] ,eax
 add sumofxsq , eax
  add edi,type time
  add esi , type time
loop next


;pearson cofficient r
;difference of mean squared
cld 
mov ecx ,lengthof table_xsubxmean 
mov esi ,offset table_xsubxmean 
mov edi , offset table_diffmeansq
loopstart:
  lodsd 
  mov ebx ,0 ; using for indexing 
  mul table_ysubymean[ebx]
  stosd
  add sumofdiff , eax
   add ebx ,4



   
loop loopstart

;r=sumofdiff / (sumofxsq *sumofysq)^1/2
mov eax ,sumofxsq

mul sumofysq
mov ebx, eax
mov eax, sumofdiff
call writeint

;taking square root
mov cx,00000h
mov bx,0ffffh
incr:add bx,02
     inc cx
     sub ax,bx
jnz incr
movsx eax,cx ; eax contain the squareroot
call writeint

;invoke xsubxmean ,addr table_xsubxmean, addr time ,lengthof time ,stockprice_mean
;call DumpRegs
exit
main ENDP
END main

ysubymean proc 
push ebp
mov ebp, esp

mov edi ,offset table_ysubymean  
mov esi ,offset stockprice ;second parameter offset of stock price array
mov ecx, lengthof stockprice; second parameter number of elements in stockprice
mov ebx,stockprice_mean;fourth perimeter

start:
mov eax ,[esi]
mov [edi],eax
sub [edi],ebx

add edi,type stockprice
add esi , type stockprice

loop start 

pop ebp
ret 16

ysubymean endp 


xsubxmean proc uses esi edi eax, ptabley :ptr dword, pstock:ptr dword ,l :dword ,mean:dword

;push ebp
;mov ebp, esp
enter 0,0
mov edi , ptabley
mov esi ,pstock ;first parameter offset of stock price array
mov ecx, l ; second parameter number of elements in stockprice
mov ebx,mean fourth perimeter

start:
mov eax ,[esi]
mov [edi],eax
sub [edi],ebx

add edi,type stockprice
add esi , type stockprice

loop start 
exit

xsubxmean endp 


