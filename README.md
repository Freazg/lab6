## lab6

# test_sum_8bit.v

module test_sum_8bit;
    reg [7:0] Ain_r, Bin_r;
    reg Ci_r;
    wire [7:0] Ain, Bin;
    wire Ci, cm, cr;
    wire [7:0] res_my, res_ref;

    my_sum_8bit my_block(Ain, Bin, Ci, res_my, cm);
    ref_sum_8bit ref_block(Ain, Bin, Ci, res_ref, cr);

    assign Ain = Ain_r;
    assign Bin = Bin_r;
    assign Ci = Ci_r;

    initial begin
        $display("Time Ain Bin Ci res_my cm res_ref cr");
        $monitor("%0t Ain=%b Bin=%b Ci=%b res_my=%b cm=%b res_ref=%b cr=%b", $time, Ain, Bin, Ci, res_my, cm, res_ref, cr);
        #400 $finish;
    end

    initial begin
        Ain_r = 8'h01; #50 Ain_r = 8'h05; #50 Ain_r = 8'h01; #50 Ain_r = 8'h05; #50 Ain_r = 8'h01; #50 Ain_r = 8'h05;
    end

    initial begin
        Bin_r = 8'h02; #100 Bin_r = 8'h0A; #100 Bin_r = 8'h02; #100 Bin_r = 8'h0A;
    end

    initial begin
        Ci_r = 1'b0; #200 Ci_r = 1'b1;
    end
endmodule

# ref_sum_8bit.v

module ref_sum_8bit(
    input [7:0] Ain, Bin,
    input Ci,
    output [7:0] Sout,
    output Co
);
    wire [7:0] Ain, Bin;
    reg [8:0] S;
    always @ (Ain, Bin, Ci)
        S = Ain + Bin + Ci;
    assign Sout = S[7:0];
    assign Co = S[8];
endmodule

# my_sum_8bit.v

module my_sum_8bit(
    input [7:0] Ain, Bin,
    input Ci,
    output [7:0] Sout,
    output Co
);
    wire [7:0] C;
    bitsum sum0(Ain[0], Bin[0], Ci, Sout[0], C[0]);
    bitsum sum1(Ain[1], Bin[1], C[0], Sout[1], C[1]);
    bitsum sum2(Ain[2], Bin[2], C[1], Sout[2], C[2]);
    bitsum sum3(Ain[3], Bin[3], C[2], Sout[3], C[3]);
    bitsum sum4(Ain[4], Bin[4], C[3], Sout[4], C[4]);
    bitsum sum5(Ain[5], Bin[5], C[4], Sout[5], C[5]);
    bitsum sum6(Ain[6], Bin[6], C[5], Sout[6], C[6]);
    bitsum sum7(Ain[7], Bin[7], C[6], Sout[7], C[7]);
    assign Co = C[7];
endmodule

module bitsum(
    input A, B, Cin,
    output S, Cout
);
    wire Res, c1, c2;
    xor(Res, A, B);
    and(c1, A, B);
    xor(S, Cin, Res);
    and(c2, Cin, Res);
    or(Cout, c1, c2);
endmodule
