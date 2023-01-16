module Half_Adder(A,B,S,Cout);
input A,B;
output S,Cout;
assign S=(A^B);
assign Cout=(A&B);
endmodule

module RCA(A,inc,Sum,Cout);
input [3:0] A;
input inc;
output [3:0] Sum;
output Cout;
wire c1,c2,c3;
Half_Adder ha0(A[0], inc, Sum[0], c1);
Half_Adder ha1(A[1], c1, Sum[1], c2);
Half_Adder ha2(A[2], c2, Sum[2], c3);
Half_Adder ha3(A[3], c3, Sum[3], Cout);
endmodule

module DFF0(data_in,clock,reset, data_out);
input data_in;
input clock,reset;
output reg data_out;
always@(posedge clock)
begin
if(reset)
data_out<=1'b0;
else
data_out<=data_in;
end
endmodule

module DFF4(In,clk,reset,Out);
input [3:0]In;
input clk,reset;
output [3:0]Out;
DFF0 df0(In[0],clk,reset,Out[0]);
DFF0 df1(In[1],clk,reset,Out[1]);
DFF0 df2(In[2],clk,reset,Out[2]);
DFF0 df3(In[3],clk,reset,Out[3]);
endmodule

module count10(clock, inc, reset, Count, count_eq_9);
input clock,inc,reset;
output [3:0]Count;
output count_eq_9;
wire [3:0]fourout;
wire carry;
assign count_eq_9=((Count==4'b1001)?1:0);
assign rst=((count_eq_9 & inc) | reset);
RCA rca(Count, inc, fourout, carry);
DFF4 df(fourout,clock,rst, Count);
endmodule

module count6(clock, inc, reset, Count, count_eq_6);
input clock,inc,reset;
output [3:0]Count;
output count_eq_6;
wire [3:0]fourout;
wire carry;
assign count_eq_6=((Count==4'b0101)?1:0);
assign rst=((count_eq_6 & inc) | reset);
RCA rca(Count, inc, fourout, carry);
DFF4 df(fourout,clock,rst, Count);
endmodule

module clk_divider(clock, rst, clk_out);
input clock, rst;
output clk_out;
wire [18:0] din;
wire [18:0] clkdiv;
DFF0 dff_inst0(
.data_in(din[0]),
.clock(clock),
.reset(rst),
.data_out(clkdiv[0])
);
genvar i;
generate
for (i = 1; i < 19; i=i+1)
begin : dff_gen_label
DFF0 dff_inst (
.data_in (din[i]),
.clock(clkdiv[i-1]),
.reset(rst),
.data_out(clkdiv[i])
);
end
endgenerate
assign din = ~clkdiv;
assign clk_out = clkdiv[18];
endmodule

module BCDto7S(I,A,B,C,D,E,F,G);
input [3:0] I; //BCD Input
output A,B,C,D,E,F,G;
assign A=(I[2]&~I[1]&~I[0])|(~I[3]&~I[2]&~I[1]&I[0]);
assign B=(I[2]&~I[1]&I[0])|(I[2]&I[1]&~I[0]);
assign C=~I[2]&I[1]&~I[0];
assign D=(I[2]&~I[1]&~I[0])|(~I[2]&~I[1]&I[0])|(I[2]&I[1]&I[0]);
assign E=I[0]|(I[2]&~I[1]);
assign F=(I[1]&I[0])|(~I[3]&~I[2]&I[0])|(~I[2]&I[1]);
assign G=(I[2]&I[1]&I[0])|(~I[3]&~I[2]&~I[1]);
endmodule

module final(clock,inc,reset,a1,b1,c1,d1,e1,f1,g1,ceq9out);
input clock;
input inc;
input reset;
output a1,b1,c1,d1,e1,f1,g1;
output ceq9out;
wire clk_out;
wire [3:0]O1;
//wire dangling;
clk_divider clkdiv(clock, 0, clk_out);
count10 c10(clk_out, inc, reset, O1, ceq9out);
BCDto7S bcd(O1,a1,b1,c1,d1,e1,f1,g1);
endmodule

module final6(clock,inc,reset,a1,b1,c1,d1,e1,f1,g1,ceq6out);
input clock;
input inc;
input reset;
output ceq6out;
output a1,b1,c1,d1,e1,f1,g1;
wire clk_out;
wire [3:0]O1;
//wire dangling;
clk_divider clkdiv(clock, 0, clk_out);
count6 c6(clk_out, inc, reset, O1,ceq6out);
BCDto7S bcd(O1,a1,b1,c1,d1,e1,f1,g1);
endmodule

module TFF0 (
data , // Data Input
clk , // Clock Input
reset , // Reset input
q // Q output
);
//-----------Input Ports---------------
input data, clk, reset ;
//-----------Output Ports---------------
output q;
//------------Internal Variables--------
reg q;
//-------------Code Starts Here---------
always @ ( posedge clk or posedge reset)
if (reset) begin
q <= 1'b0;
end else if (data) begin
q <= !q;
end
endmodule

module stopwatch(reset, stop, clock, a, b, c, d);
input reset, stop, clock;
wire clk_out;
wire Q;
wire inc1, inc2, inc3;
wire [2:0]ceq9out;
output [6:0]a;
output [6:0]b;
output [6:0]c;
output [6:0]d;
wire ceq6out;
//clk_divider clkdiv(clock, 0, clk_out);
TFF0 tff(1,stop,0,Q);
assign inc1 = (ceq9out[0] & Q);
assign inc2 = (ceq9out[1] & inc1);
assign inc3 = (ceq9out[2] & inc2);
final6 Atime(clock,inc3,reset,a[0], a[1], a[2], a[3], a[4], a[5], a[6],ceq6out);
final Btime(clock,inc2,reset,b[0], b[1], b[2], b[3], b[4], b[5], b[6],ceq9out[2]);
final Ctime(clock,inc1,reset,c[0], c[1], c[2], c[3], c[4], c[5], c[6],ceq9out[1]);
final Dtime(clock,Q,reset,d[0], d[1], d[2], d[3], d[4], d[5], d[6],ceq9out[0]);
endmodule
