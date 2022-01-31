## Variables in Unamed Block
- In verilog 2001, only named block can declare local variables
- In Systemverilog, unmaed block can declare local variables
* The variable is visible  in the block where it is declared and in any nested blocks
* Hierachical referecens to variable cannot be used (hide var from outside hierachy ref)

```
intial begin			//Unmaed block
 integer i;			//Local declaration in unamed block
 i = 0;
 while(i<=10) begin		//Unamed block
  $display("i:%d",i);
  if(i<5) begin			//Unamed block
   $display("Number is:%d",i);
  end
  i = i+1;
 end
end
```

## FOR loop enhancement
- You can declare a for loop variable within for statement, vars are visible only in the loop, Same identifier name can be safely used in multiple (un-nested) loops
```
//SV
initila begin
//loop variable declared in loop
for (int i = 0; i<10; i++)
 ...

initial begin
for (int i = 0; i<8; i++)			// Two loops, two variables (diffrent i)
...
```

```
//Verilog
//loop variable declared outside loop
integer i;
intial begin			
 for(i=0; i<10;i++)
 ...

intial begin					//Two loops, 1 variable (same i)
 for(i=0;i<8;i++)
 ...
```

## do..while Loop

```
...
do
 @(posedge clk)
 count = count + 1;
while (enable);
...
```

## foreach Loop
This loop iterates over all the elements of an array
Loop variable characteristics:
- Does not have to be declared
- Read only
- Only visible inside loop
Use multiple loop vars for multidimensional arrays
- Equivalent to nested loops
Useful for initializing and array processing
```
int intarr[7:0]
...
foreach(intarr[i])
 intarr[i] = 7-i;			//Iterates left bound to right bound

============EQUIVALENT BELOW==============
int intarr[7:0]
...
for(int i = 7; i>=0; i=i-1)
 intarr[i] = 7-i;
```
### 2 loop vars for 2D array cretes nested loops
```
int arr2d [7:0] [2:0];
...
foreach(intarr [k,l])
 arr2d[k][l] = k*l;
```

## Verilog case statement Review
Branches in a case statement are prioritized :
- Checked in order
- Branch condition can overlap
- Synthesize to priority encoder
Matually exclusive branches should infer nonpriority logic:
- May need synthesis directives to ensure this - parallel_case
```
(*synthesis parallel_case*)
case (parc)
 0:   op = a;
 1,2  op = b;
 3:   op = c;
endcase
```

In full_case: all possible value of case expression have a branch

- When you write the combinational logic such that you can't deduce a output with present inputs, synthesis tool infers a latch
- Same thing in case statement inside combinational logic, there some combinational input there for which you have not specified the value for one or more output, that one/more output get latch.

```
(*synthesis parallel_case, full_case*)			//Synthesis attribute full_case tell synthesis tool that assume I have the default case statement
case(fullc)						//Everything inside case statement is match
 0,1 :   op = a;
 2:      op = b;
 default:op = c;					//Would get latch when fullc eq 3 unless put EITHER `default` statement OR mention `full_case` synthesis attribute
endcase
```
* casex take into account `Z and X and ?` for the bit position that should not participate in match
* casez take into account `Z and ?` for the bit position that should not participate in match
