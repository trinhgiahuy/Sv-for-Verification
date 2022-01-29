## Verilog data type rules
* ***Variables(registers)*** (integer, real, reg, time) are assigned value only in procedural block
* ***Nets*** are driven by continuous assignments, module input, module instance output or primitive instance

### Following connectivity characteristic
* Module inputs are always ***nets***
* Module outputs are ***variables*** if driven by a procedure block, or ***nets*** in all other case
* Connection to the input ports of a module instance are ***VARIABLE if driven by a procedure block, or NETS in all other case***
* Connection to output ports of a module instance are always ***NETS***
* Connections to bidirectional inout ports are always ***NETS***

Verilog require input ports to be declared as nets , the output of module is not necessarily a variable but it might be variable. 
Because it might be a variable, connection on high side has to be a net. Net is the one which can resolve values that are coming from multiple places
You are not allowed to have variable back-to-back to another variable

