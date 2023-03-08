
# Exams/2014 q3fsm solution

Consider a finite state machine with inputs s and w. Assume that the FSM begins in a reset state called A, as depicted below. The FSM remains in state A as long as s = 0, and it moves to state B when s = 1. Once in state B the FSM examines the value of the input w in the next three clock cycles. If w = 1 in exactly two of these clock cycles, then the FSM has to set an output z to 1 in the following clock cycle. Otherwise z has to be 0. The FSM continues checking w for the next three clock cycles, and so on. The timing diagram below illustrates the required values of z for different values of w.
<br><br>
Use as few states as possible. Note that the s input is used only in state A, so you need to consider just the w input.
<br><br>
![alt text](https://github.com/na103/2014q3fsm/blob/main/img/wavedrom.png "wavedrom")
<br>
![alt text](https://github.com/na103/2014q3fsm/blob/main/img/Exams_2014q3fsm.png "state")
<br><br>
My compact solution to the [problem](https://hdlbits.01xz.net/wiki/Exams/2014_q3fsm) involves the use of a FSM without the need of additional counters or registers:
<br><br>
# State diagram
![alt text](https://github.com/na103/2014q3fsm/blob/main/img/Immagine.png "diagram")
## State-transition table

|  State     |  w=1 <br> next_state/z  |   w=0 <br> next_state/z   |
|:-----:|-----------------|------------------|
|S0     |      S1/0       |       S2/0       |
|S1     |      S3/0       |       S4/0       |
|S2     |      S4/0       |       S5/0       |
|S3     |      S0/0       |       S6/0       |
|S4     |      S6/0       |       S7/0       |
|S5     |      S7/0       |       S7/0       |
|S6     |      S1/1       |       S2/1       |
|S7     |      S1/0       |       S2/0       |
                              

## Verilog code
```verilog
module top_module (
    input clk,
    input reset,   // Synchronous reset
    input s,
    input w,
    output z
);
    
    parameter IDLE=4'd0, S0=4'd1, S1=4'd2, S2=4'd3, S3=4'd4, S4=4'd5, S5=4'd6, S6=4'd7, S7=4'd8;
    reg [3:0] state, next_state;
    
    always @(*) begin
        case(state)
            IDLE: next_state = s ? S0:IDLE;
            S0: next_state = w ? S1:S2;
            S1: next_state = w ? S3:S4;
            S2: next_state = w ? S4:S5;
            S3: next_state = w ? S0:S6;
            S4: next_state = w ? S6:S7;
            S5: next_state = S7;
            S6: next_state = w ? S1:S2;
            S7: next_state = w ? S1:S2;
            default next_state = 4'bx;
        endcase
    end
    
    always @(posedge clk) begin
        if(reset)
            state <= IDLE;
        else
            state <= next_state;
    end
    
    assign z = state == S6;
    
endmodule
```

# License

This work is licensed under a Creative Commons Attribution 4.0 International License. See [https://creativecommons.org/licenses/by/4.0/](https://creativecommons.org/licenses/by/4.0/).
