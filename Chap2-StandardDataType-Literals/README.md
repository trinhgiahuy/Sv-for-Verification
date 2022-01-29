## Verilog data type rules
* ***Variables(registers)*** (integer, real, reg, time) are assigned value only in procedural block
* ***Nets*** are driven by continuous assignments, module input, module instance output or primitive instance

### Following connectivity characteristic
* Module inputs are always ***NETS***
* Module outputs are ***VARIABLES*** if driven by a procedure block, or ***NETS*** in all other case
* Connection to the input ports of a module instance are ***VARIABLE if driven by a procedure block, or NETS in all other case***
* Connection to output ports of a module instance are always ***NETS***
* Connections to bidirectional inout ports are always ***NETS***

Verilog require input ports to be declared as nets , the output of module is not necessarily a variable but it might be variable. 
Because it might be a variable, connection on high side has to be a net. Net is the one which can resolve values that are coming from multiple places
You are not allowed to have variable back-to-back to another variable

## Relaxation of Data type in SystemVerilog
You can assing a SV variables
* In any number of **initial** or **always** block
* From a single continuous assignment
* From a single module out port
* From a single primitive output
=> Thus you can declare most design signal to be variable, as **var** keyword is optional, simply declare as type **logic**

**RESTRICTION**
prevent multiple drivers on a singple variable
- You cannot combine procedure assignment with continuous assignment OR with module output drivers on the same variable
- You cannot have multiple continuous assignments or multiple output ports drive the same variable
- Only **NET** type can have multiple drivers

```
module test;
 logic in1, in2, in3;
 logic op;
 mone(op,in1,in2);        
 mtwo(op,in1,ỉn); 		// ERROR: Multiple output drivers on logic op
// SHOULD BE : wire op;
 
endmodule
```

## Multiple Procedure Assignment
### Problem
- Verilog allow assignments to a variables from multiple procedure blocks (Since in Verilog, variables are assigned, not driven)
### Solution
- Special procedure blocks allow only a singple driver to variables.

SV have new sets of always block: always_comb, always_latch, always_ff block

```
logic op;

always_comb
 if(sel1)
  op = a;
 else
  op = b;
				//ERROR: always_comb allow only a signple driver on logic op
always_comb
 if(sel2)
  op = c;
 else
  op = d;
```

SV not restrict Verilog schematic, if you use always block, it still works the way it works
```
logic op;

always @(sel1,a,b)
 if(sel1)
  op = a;
 else
  op = b;
				
always @(sel2,c,d)		// NORMAL, but the variable can synthesize as combinational logc, latch logic or sequential logic				
 if(sel2)			// with warning assumption, depending how you use it
  op = c;
 else
  op = d;
``` 

## 2-State Data Types

All Verilog data type are four state logic except real and realtime

|Type		| Descriptio											| Sign(Default)|
|---------------|-----------------------------------------------------------------------------------------------|--------------|
|bit		| Single bit,scalable to vector									| Unsigned     |
|byte		| 8-bit vector or ASCII character								| Signed       |
|shortint	| 16-bit vector											| Signed       |
|int		| 32-bit vector											| Signed       |
|longint	| 64-bit vector											| Signed       |

- Based on bit, value of 0 & 1 only
- Direct replacemnets for reg, logic or integer
- Greater effciency at higher-abstraction-level modeling (RTL)

## Unsized Literal

```
// Verilog
logic [5:0] databus;

databus = 6'b0;		//000000
databus = 6'b1;		//000001
databus = 6'bz;		//zzzzzz
databus = 4'bx;		//00xxxx


//-----------------
//SystemVerilog
logic [5:0] databus;

databus = '0;		//000000
databus = '1;		//111111
databus = 'z;		//zzzzzz
databus = 'x;		//xxxxxx
```

## Time Literals
SV provide time literal construct that
- Explicitly specifies time value
- Allows more control over time literal
Format:
- Integer or fixed-point value
- Immediately followed by time units without space between them (fs ps ns us s)
- Scaled to current precision
- **1step** delay value: one simulation precision unit

```
// #1step advance simulation 100ps in this example

`timescale 1ns/100ps			// time unit 1ns , precision 100ps
module testmodule
 logic avar, sel, bvar, cvar, clk;

 initial
 begin
  #20ns sel =  0;
  #5.18ns sel = 1;		// here rounded to 5.2ns
  bvar = #1step cvar		// blocking assigin using 1step
 end
 
always @(posedge clk)
 #5.1ns avar <= avar + 1;     // Non-blocking assign

endmodule
```
