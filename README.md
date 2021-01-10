# 羅設第13組final project
這邊是程式碼
```
module LD_final_project(output reg [7:0] DATA_R, DATA_G, DATA_B,
								output reg [6:0] d7_1, 
								output reg [2:0] COMM, Life,
								output reg [1:0] COMM_CLK,
								output EN,
								input CLK, clear, Left, Right);
	reg [7:0] plate [7:0];
	reg [7:0] people [7:0];
	reg [6:0] seg1, seg2;
	reg [3:0] bcd_s,bcd_m;
	reg [2:0] random01, random02, random03, r, r1, r2;
	reg left, right, temp;
	segment7 S0(bcd_s, A0,B0,C0,D0,E0,F0,G0);
	segment7 S1(bcd_m, A1,B1,C1,D1,E1,F1,G1);
	divfreq div0(CLK, CLK_div);
	divfreq1 div1(CLK, CLK_time);
	divfreq2 div2(CLK, CLK_mv);
	byte line, count, count1;
	integer a, b, c, touch;

//初始值
	initial
		begin
			bcd_m = 0;
			bcd_s = 0;
			line = 3;
			random01 = (5*random01 + 3)%16;
			r = random01 % 8;
			random02 = (5*(random02+1) + 3)%16;
			r1 = random02 % 8;
			random03= (5*(random03+2) + 3)%16;
			r2 = random03 % 8;
			a = 0;
			b = 0;
			c = 0;
			touch = 0;
			DATA_R = 8'b11111111;
			DATA_G = 8'b11111111;
			DATA_B = 8'b11111111;
			plate[0] = 8'b11111111;
			plate[1] = 8'b11111111;
			plate[2] = 8'b11111111;
			plate[3] = 8'b11111111;
			plate[4] = 8'b11111111;
			plate[5] = 8'b11111111;
			plate[6] = 8'b11111111;
			plate[7] = 8'b11111111;
			people[0] = 8'b11111111;
			people[1] = 8'b11111111;
			people[2] = 8'b11111111;
			people[3] = 8'b00111111;
			people[4] = 8'b11111111;
			people[5] = 8'b11111111;
			people[6] = 8'b11111111;
			people[7] = 8'b11111111;
			count1 = 0;
		end

		
//7段顯示器的視覺暫留
always@(posedge CLK_div)
	begin
		seg1[0] = A0;
		seg1[1] = B0;
		seg1[2] = C0;
		seg1[3] = D0;
		seg1[4] = E0;
		seg1[5] = F0;
		seg1[6] = G0;
		
		seg2[0] = A1;
		seg2[1] = B1;
		seg2[2] = C1;
		seg2[3] = D1;
		seg2[4] = E1;
		seg2[5] = F1;
		seg2[6] = G1;
		
		if(count1 == 0)
			begin
				d7_1 <= seg1;
				COMM_CLK[1] <= 1'b1;
				COMM_CLK[0] <= 1'b0;
				count1 <= 1'b1;
			end
		else if(count1 == 1)
			begin
				d7_1 <= seg2;
				COMM_CLK[1] <= 1'b0;
				COMM_CLK[0] <= 1'b1;
				count1 <= 1'b0;
			end
		
		


	end

//計時&進位	
always@(posedge CLK_time, posedge clear)
	begin
		if(clear)
			begin
				bcd_m = 3'b0;
				bcd_s = 4'b0;
			end
		else
			begin
				if(touch < 3)
					begin
						if(bcd_s >= 9)
							begin
								bcd_s <= 0;
								bcd_m <= bcd_m + 1;
							end
						else
							bcd_s <= bcd_s + 1;
						if(bcd_m >= 9) bcd_m <= 0;
					end
			end
	end

//主畫面的視覺暫留	
always@(posedge CLK_div)
	begin
		if(count >= 7)
			count <= 0;
		else
			count <= count + 1;
		COMM = count;
		EN = 1'b1;
		if(touch < 3)
			begin
				DATA_G <= plate[count];
				DATA_R <= people[count];
				if(touch == 0)
					Life <= 3'b111;
				else if(touch == 1)
					Life <= 3'b110;
				else if(touch == 2)
					Life <= 3'b100;
			end
		else
			begin
				DATA_R <= plate[count];
				DATA_G <= 8'b11111111;
				Life <= 3'b000;
			end
	end

	
//遊戲
always@(posedge CLK_mv)
	begin
		
		right = Right;
		left = Left;	
		
		if(clear == 1)
			
				begin

					touch = 0;
					line = 3;
					a = 0;
					b = 0;
					c = 0;
					random01 = (5*random01 + 3)%16;
					r = random01 % 8;
					random02 = (5*(random02+1) + 3)%16;
					r1 = random02 % 8;
					random03= (5*(random03+2) + 3)%16;
					r2 = random03 % 8;
					plate[0] = 8'b11111111;
					plate[1] = 8'b11111111;
					plate[2] = 8'b11111111;
					plate[3] = 8'b11111111;
					plate[4] = 8'b11111111;
					plate[5] = 8'b11111111;
					plate[6] = 8'b11111111;
					plate[7] = 8'b11111111;
					people[0] = 8'b11111111;
					people[1] = 8'b11111111;
					people[2] = 8'b11111111;
					people[3] = 8'b00111111;
					people[4] = 8'b11111111;
					people[5] = 8'b11111111;
					people[6] = 8'b11111111;
					people[7] = 8'b11111111;
					
				end
////////////////////////////////////////
			//fall object 1
		if(touch < 3)
			begin
				if(a == 0)
					begin
						plate[r][a] = 1'b0;
						a = a+1;
					end
				else if (a > 0 && a <= 7)
						begin
							plate[r][a-1] = 1'b1;
							plate[r][a] = 1'b0;
							a = a+1;
						end
				else if(a == 8) 
					begin
						plate[r][a-1] = 1'b1;
						random01 = (5*random01 + 3)%16;
						r = random01 % 8;
						a = 0;
					end
/////////////////////////////////////////	
			//fall object 2
				if(b == 0)
					begin
						plate[r1][b] = 1'b0;
						b = b+1;
					end
				else if (b > 0 && b <= 7)
					begin
						plate[r1][b-1] = 1'b1;
						plate[r1][b] = 1'b0;
						b = b+1;
					end
				else if(b == 8) 
					begin
						plate[r1][b-1] = 1'b1;
						random02 = (5*(random01+1) + 3)%16;
						r1 = random02 % 8;
						b = 0;
					end
/////////////////////////////////////////		
			//fall object 3
				if(c == 0)
					begin
						plate[r2][c] = 1'b0;
						c = c+1;
					end
				else if (c > 0 && c <= 7)
					begin
						plate[r2][c-1] = 1'b1;
						plate[r2][c] = 1'b0;
						c = c+1;
					end
				else if(c == 8) 
					begin
						plate[r2][c-1] = 1'b1;
						random03= (5*(random01+2) + 3)%16;
						r2 = random03 % 8;
						c = 0;
					end
/////////////////////////////////////////	
			//people move		
				if((right == 1) && (line != 7))
					begin
						people[line][6] = 1'b1;
						people[line][7] = 1'b1;
						line = line + 1;
					end
				if((left == 1) && (line != 0))
					begin
						people[line][6] = 1'b1;
						people[line][7] = 1'b1;
						line = line - 1;
					end
				people[line][6] = 1'b0;
				people[line][7] = 1'b0;
		
				if(plate[line][6] == 0)
					begin
						touch = touch + 1;
						plate[r][6] = 1'b1;
						
						plate[r1][6] = 1'b1;
						
						plate[r2][6] = 1'b1;
						
						a = 8;
						b = 8;
						c = 8;
					end
				else if (plate[line][7] == 0)
					begin
						touch = touch + 1;
												
						plate[r][7] = 1'b1;
						
						plate[r1][7] = 1'b1;
						
						plate[r2][7] = 1'b1;
						a = 8;
						b = 8;
						c = 8;
						
					end
					
				
			end
			//game over ---> X
		else
			begin
				plate[0] = 8'b01111110;
				plate[1] = 8'b10111101;
				plate[2] = 8'b11011011;
				plate[3] = 8'b11100111;
				plate[4] = 8'b11100111;
				plate[5] = 8'b11011011;
				plate[6] = 8'b10111101;
				plate[7] = 8'b01111110;
			end
	////////////////if///////////////		
				 if(bcd_m==2) 
				 begin
				plate[0] = 8'b01110111;
				plate[1] = 8'b01101011;
				plate[2] = 8'b01011101;
				plate[3] = 8'b00111110;
				plate[4] = 8'b00111110;
				plate[5] = 8'b01011101;
				plate[6] = 8'b01101011;
				plate[7] = 8'b01110111;
				end
	end
	
endmodule


//秒數轉7段顯示器
module segment7(input [0:3] a, output A,B,C,D,E,F,G);


	
	assign A = ~(a[0]&~a[1]&~a[2] | ~a[0]&a[2] | ~a[1]&~a[2]&~a[3] | ~a[0]&a[1]&a[3]),
	       B = ~(~a[0]&~a[1] | ~a[1]&~a[2] | ~a[0]&~a[2]&~a[3] | ~a[0]&a[2]&a[3]),
			 C = ~(~a[0]&a[1] | ~a[1]&~a[2] | ~a[0]&a[3]),
			 D = ~(a[0]&~a[1]&~a[2] | ~a[0]&~a[1]&a[2] | ~a[0]&a[2]&~a[3] | ~a[0]&a[1]&~a[2]&a[3] | ~a[1]&~a[2]&~a[3]),
			 E = ~(~a[1]&~a[2]&~a[3] | ~a[0]&a[2]&~a[3]),
			 F = ~(~a[0]&a[1]&~a[2] | ~a[0]&a[1]&~a[3] | a[0]&~a[1]&~a[2] | ~a[1]&~a[2]&~a[3]),
			 G = ~(a[0]&~a[1]&~a[2] | ~a[0]&~a[1]&a[2] | ~a[0]&a[1]&~a[2] | ~a[0]&a[2]&~a[3]);
			 
endmodule


		
//視覺暫留除頻器
module divfreq(input CLK, output reg CLK_div);
  reg [24:0] Count;
  always @(posedge CLK)
    begin
      if(Count > 5000)
        begin
          Count <= 25'b0;
          CLK_div <= ~CLK_div;
        end
      else
        Count <= Count + 1'b1;
    end
endmodule

//計時除頻器
module divfreq1(input CLK, output reg CLK_time);
  reg [25:0] Count;
  initial
    begin
      CLK_time = 0;
	 end	
		
  always @(posedge CLK)
    begin
      if(Count > 25000000)
        begin
          Count <= 25'b0;
          CLK_time <= ~CLK_time;
        end
      else
        Count <= Count + 1'b1;
    end
endmodule 

//掉落物&人物移動除頻器
module divfreq2(input CLK, output reg CLK_mv);
  reg [35:0] Count;
  initial
    begin
      CLK_mv = 0;
	 end	
		
  always @(posedge CLK)
    begin
      if(Count > 4500000)
        begin
          Count <= 45'b0;
          CLK_mv <= ~CLK_mv;
        end
      else
        Count <= Count + 1'b1;
    end
endmodule 
```
## 這是pin角
```
COMM[1]	Location	PIN_129
COMM[2]	Location	PIN_132
DATA_B[0]	Location	PIN_135
DATA_B[1]	Location	PIN_136
DATA_B[2]	Location	PIN_137
DATA_B[3]	Location	PIN_138
DATA_B[4]	Location	PIN_141
DATA_B[5]	Location	PIN_142
DATA_B[6]	Location	PIN_143
DATA_B[7]	Location	PIN_144
DATA_G[0]	Location	PIN_72
DATA_G[1]	Location	PIN_73
DATA_G[2]	Location	PIN_74
DATA_G[3]	Location	PIN_75
DATA_G[4]	Location	PIN_76
DATA_G[5]	Location	PIN_77
DATA_G[6]	Location	PIN_79
DATA_G[7]	Location	PIN_80
DATA_R[0]	Location	PIN_64
DATA_R[1]	Location	PIN_65
DATA_R[2]	Location	PIN_66
DATA_R[3]	Location	PIN_67
DATA_R[4]	Location	PIN_68
DATA_R[5]	Location	PIN_69
DATA_R[6]	Location	PIN_70
DATA_R[7]	Location	PIN_71
EN	Location	PIN_133
Left	Location	PIN_126
Right	Location	PIN_127
COMM[0]	Location	PIN_128
CLK	Location	PIN_22
clear	Location	PIN_125
Life[1]	Location	PIN_99
Life[2]	Location	PIN_98
Life[0]	Location	PIN_100
d7_1[1]	Location	PIN_112
d7_1[2]	Location	PIN_113
d7_1[3]	Location	PIN_114
d7_1[4]	Location	PIN_115
d7_1[5]	Location	PIN_119
d7_1[6]	Location	PIN_120
d7_1[0]	Location	PIN_111
COMM_CLK[1]	Location	PIN_106
COMM_CLK[0]	Location	PIN_110
```
