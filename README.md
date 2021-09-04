# Overview RTL_design_workshop
This repo contain all of my files and documentation of the workshop "RTL design using Verilog with SKY130 Technology"

-----------------------------------------------------------------------------------------------------------------------------------------------
# Day 1
    
Before starting with the first lab is necessary to standardize some concepts.

- Simulator: will be the tool used for simulating the RTL designs. Trought this course, we will use iverilog

- Design: Can be one or more Verilog files that describe the functionality of some circuit based on a set of specifications 

- TestBench: Is a Verilog file that contains a set of stimulus (called test vectors) to check the functionality of my design


## How simulator works? (can be iverilog or other commercials simulators)

The simulator looks for the changes on the input signals. When one input (or more) change, the output is evaluated
- remember: if any change occurs in the input, will be any changes on the output.
*Every design has one or more Primary inputs and Primary outputs.*

A classical testbench look like the next figure:

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/test_bench.png?raw=true)

Where the "My design" is a verilog file that contains an digital circuit, and this circuit is instantiated in the test bench verilog file for aplicate stimulus to the inputs of My design and capture the changes on the outputs 

## Lab1 Day 1  (initial respositories and common files for the next labs)

To start the day one lab is necessary to clone some repositories. So on the terminal, write the following commands: 

    git clone https://github.com/kunalg123/vsdflow.git
    git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
    
This commands will create two directories.

On the directory ‌``` /sky130RTLDesignAndSynthesisWorkshop``` you will have two subdirectories. One is called my_lib and contains another two subdirectories ``` /lib ``` and ``` /verilog_model```

The subdir ``` /lib ``` contains the sky130 standar cell library for the synthesis process, and the name of this lib is sky130_fd_sc_hd__tt_025C_1v80.lib

The subdir ``` /verilog_model```  contains all of the standar cell verilog model

The directory ``` /sky130RTLDesignAndSynthesisWorkshop/verilog_files``` contains all of the verilog files for the labs experiments (verilog designs and test benchs)

## Lab2 Day 1 (iverilog and gtkwave)
Inside the directory /sky130RTLDesignAndSynthesisWorkshop/verilog_files we have a lot of .v files. This files are designs and test bench. 
The files that start whit "tb_.." are the test bench files. For example one of the design on this chapter is good_mux.v and the respective testbench is tb_good_mux.tb.
In the next picture it is possible to see all of the verilog files inside of the the directory ``` /sky130RTLDesignAndSynthesisWorkshop/verilog_files```

### Simulating using Iverilog

For do a simulation on iverilog will be use one of the design files of the /sky130RTLDesignAndSynthesisWorkshop/verilog_files and his respective tesbench file. 
The flow of simulation is graphically showed in the next figure

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/TB.png?raw=true)

The command and sintaxis for do this is:

 ```iverilog good_mux.v tb_good_mux.v``` 

If everithing is good the last command will be create and executable binary file call a.out that you need to run doing
./a.out  

You will see the next message: VCD info: dumpfile tb_good_mux.vcd opened for output
The past steps are shown in the next figure

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/3.jpg?raw=true)

Now we can use gtkwave for see the waveforms of the simulation. Run gtkwave tb_good_mux.vcd 
gtkwave gui will be open and you can do a drag and drop method for import the signals to the viewer.
An example of this is in the next figure:

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/gtk.jpg?raw=true)

## Lab3 Day 1 (inside to the verilog files)

If we look what contains the .v files used in the Lab2 we will see the structure of the design and the testbench of this design.
For see this run on terminal (in the same directory of the .v files) 
gvim good_mux.v -o tb_good_mux.v 
This will show you an editor and you can see what are writed inside of this files.

The file called good_mux.v describe a tipical 2-1 mux. I the folowing figure is the structure of this file is described
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/1.png?raw=true)
The file called tb_good_mux.v describe a testbench for the 2-1 mux. I the folowing figure is the structure of this file is described
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/2.png?raw=true)

## Introduction to yosys

yosys is a synthesizer. But what it is a synthesizer?
**Synthesizer is a tool that convert a RTL file to netlist** and this will be the synthesizer used in this workshop.

For convert RTL to netlist, the synthesizer requieres a library file (.lib file). 
- The netlist is a representation of the RTL design in standar cell of the .lib file 

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/yosys.png?raw=true)

### RTL Design
- Is a beahvioral representation of the requiered specifications in form of code (HDL representation)
an example of RTL code can be the next(register): 

```verilog
module REG(clk,rst,ce, D, Q);
	parameter n=16; // Parameter
  input wire [n-1:0] D;
	input wire rst,clk,ce;
  output reg [n-1:0] Q;

  always @(posedge clk)
	begin
      if(rst) Q<=0;
		else 
          if(ce)
			Q<=D;
	end

endmodule
```

but we need a digital circuit representation of the said code. So, how to do that?
**The process to convert an RTL code to digital circuit representation is called synthesis**

Synthesis process do:
- RTL to Gate level translation
- The design is converted into gates and the connections are made between the gates
- This is given out as a file called netlist

The process for check this netlist file (**that has exactly the same primary inputs and outputs**) is doing a test bench.
The test bench file for do this is the same test bench file used for verify the RTL code. Graphically:

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/yosysTB.png?raw=true)

### The .lib file
The .lib is file that the syntesizer requieres to do the synthesis process. This .lib contains:
- Collection of logical modules
- Includes basic logic gates like AND, OR, NOT, ETC...
- Different types of the same gate (2 input AND gate, 3 input AND gate, 4 input AND gate, etc)
- Different performance types of the same gate (slow, medium, fast)

#### Why we need fast or slow cells?
All depends of the specification of the design (like power consumption, maximum area, etc)
for example, a fast circuit requieres wide transistor and this will be implicate more area and more power consumption
for other side, a circuit that requieres low power consumption need to implement narrow transistor that will have a high capacitance, low area and less power consumption.
### Ilustration of the synthesis process
In the next figure it is possible to see a graphical representation of the RTL code mapped to standar cell of an specific .lib file
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/8.jpg?raw=true)

## Laboratory (yosys basic flow)
- On the directory ``` /sky130RTLDesignAndSynthesisWorkshop/verilog_files``` run:
 ```yosys ```

Now in yosys:

- Read the .lib file. Run:
  ``` read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```
- Read the verilog design file. Run:
  ``` read_verilog good_mux.v ```
- Specify the top module of your design. Run:
  ``` synth -top good_mux```
- Generate the netlist. Run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```
- It's possible to see a graphical representation of the netlist using the command show. So, run:
 ``` show ```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/6.jpg?raw=true)
- Write the netlist file. Run:
  ``` write_verilog -noattr good_mux_netlist.v ```
- For read the netlist run:
  ``` !gvim good_mux_netlist.v ```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY1/figs/7.jpg?raw=true)
  
-----------------------------------------------------------------------------------------------------------------------------------------------
  
# Day 2. Timinig libs, hierarchical vs flat synthesis and efficient flop coding styles

## Lab 1
The name of the .lib file contains important information for the designers. They are 3 paramaters that we can see on the .lib file called "sky130_fd_sc_hd__tt_025C_1v80.lib"

  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/lib.png?raw=true)

Also, the .lib files has a lot of information(like area, leakage power, power consumption, pin capacitance, etc) of every cell of the lib file. For example, in the next figures is possible to see the leakage power of one cell called sky130_fd_sc_hd__a2111o_1 for different inputs conditions, the capacitance of some pins, and other parameters.
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/1.jpg?raw=true)
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/2.jpg?raw=true)

For different types of the same cells we can note that the parametrs of the cells are differents. For example, if we see the characteristics of different types of AND gates we will see that the parameters (like the total area, power, delay, etc) are differents. See the next figure 

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3.jpg?raw=true)

## Lab Hier synthesis vs flat synthesis
There are two types of synthesis: Hierachical and flat synthesis.

For example, consider the following RTL code:
```verilog
module sub_module2 (input a, input b, output y);
	assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
	assign y = a&b;
endmodule


module multiple_modules (input a, input b, input c , output y);
	wire net1;
	sub_module1 u1(.a(a),.b(b),.y(net1));  //net1 = a&b
	sub_module2 u2(.a(net1),.b(c),.y(y));  //y = net1|c ,ie y = a&b + c;
endmodule
```
Note that said RTL code contains two sub modules (sub_module1 and sub_module2) and a thrid module called multiple_modules where sub_module1 and sub_module2 are interconnected.

If we synthesize this RTL code following the instruccion of the Day 1: lab Laboratory (yosys basic flow)
will be obtain a hierarchical model. See the next figure.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/5.jpg?raw=true)

On the other hand it's possible to use a flag in the synthesis workflow for do a flatten sysnthesis. This flag is *flatten*
and the commands will be:

- Read the .lib file. Run:
  ``` read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  ```
- Read the verilog design file. Run:
  ``` read_verilog multilple_modules.v ```
- Specify the top module of your design. Run:
  ``` synth -top good_mux ```
- Generate the netlist. Run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```
- Flatten flag. Run:
  ``` flatten ```
-  To see a graphical representation of the netlist using the command view. So, run:
  ```  show multilple_modules ```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/6.jpg?raw=true)
- Write the netlist file. Run:
  ``` write_verilog -noattr multilple_modules_flatten.v ```
- For read the netlist run:
  ``` !gvim multilple_modules_flatten.v ```
  
  A comparation between this two types of synthesis, and the resultant netlist file, is presented in the next figure
  
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/4.jpg?raw=true)
  
 ### Sub module synthesis
 The file called multilple_modules.v contains two submodules (1 and 2) and it's possible to synthesized an specific submodule.
 This is very important in two cases:
  - When the topmodule has multiple instantiation of the same sub_module
  - In case when we are doing the classical *divide and conquer approach*

And example of how to do that is:

Suppose that we want to syntesized the sub_module1 of the RTL code called multilple_modules.v. The yosys command for do that are the following:

- Read the .lib file. Run:
  ``` read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  ```
- Read the verilog design file. Run:
  ``` read_verilog multilple_modules.v ```
- Specify the top module of your design. Run:
 ```  synth -top sub_module1 ```
- Generate the netlist. Run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  ```
-  To see a graphical representation of the netlist using the command view. So, run:
  ``` show sub_module1 ```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/7.jpg?raw=true)
- Write the netlist file. Run:
 ```  write_verilog -noattr sub_module1.v ```
- For read the netlist run:
  ``` !gvim sub_module1.v ```
  
# Day 3. Combinational and sequential optimizations

##Why Flops?

Consider a combinatinal circuit like the next figure. And remeber that every digital circuit has a propagation delay.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/2.png?raw=true)

So if the propagation delay of the gate AND is 2ns and the propagation delay of the OR gate is 1ns, we will have a glitch condition because the 1ns of the propagation delay difference. In the next time diagram this situation is showed.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/1.png?raw=true)

If we have more combinational circuits in series, we will have possible glitches, like the following image.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/3.png?raw=true)


It's possible to avoid this situation using Flops between the circuit. The flo ps will retain the information and the out of this circuit only will change in function of the clock, this granted a stable out avoiding the glitches.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/4.png?raw=true)


## Types of Flops
There are different types of flops and differents code styles for everyone. The output of the flops will change on the rising edge or in the negedge of the clock, also the output can change when a reset signal is applied. But the output change when the reset signal is applied can be synchronous, asynchronous, or both.
The differences in the code style will determine the behavior of the flop. 
The next time diagrams show two cases of flops, synchronous and asynchronous.

## Lab1: Flip Flops simulation

The next diagram shows two types of Flop, synchronous and asynchronous respectively.
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/5.png?raw=true)
  
    ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/6.png?raw=true)
    
Using iverilog and gtk wave we will simulate three types of flop. Asynchronous set and reset, synchronous reset.

for do the simulatio run:
```
$ iverilog dff_your_flop.v tb_dff_your_flop.v
$ ./a.out
$ gtkwave tb_dff_your_flop.vcd
```

the gtkwave simulations are the following

![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/1.jpg?raw=true)
    
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/2.jpg?raw=true)
        
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/3.jpg?raw=true)
    


## Lab2: FLip Flops synthesis
The same flip flop model used in the past lab called "Lab1: Flip Flops simulation" will be synthetized using yosys. The steps for that and the results are presented in the following section.
#### Asynchronous reset Flop synthesys flow
  ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read verilog dff_asyncres.v
  $ synth -top dff_asyncres
  ```
  Now we need to force the tool yosys for use only DFF, so run:
  ``` $ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  Now create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_asyncres ```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/5.jpg?raw=true)
  
#### Asynchronous set Flop synthesys flow
  ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_asyncres_set.v
  $ synth -top dff_asyncres_set
  ```
  Now we need to force the tool yosys for use only DFF, so run:
  ``` $ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  Now create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_asyncres_set ```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/6.jpg?raw=true)

#### Synchronous set Flop synthesys flow
 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_syncres.v
  $ synth -top dff_syncres
  ```
  Now we need to force the tool yosys for use only DFF, so run:
  ``` $ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  Now create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_syncres```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY2/figs/3/7.jpg?raw=true)

## Lab3: Optimization
It's not necessary to have extra harwware for do some multiplication (two base multiplication), beacuse the basic arimetic operation called shifter do it easily and efficientily (in therms of hardware).

For example, on the directory ```/verilog_files``` we have a file called mult_2.v
On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog mult_2.v
  $ synth -top mul2
  ```
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show mul2```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/8.jpg?raw=true)
  Write the netlist, run:
  ```$ write_verilog -noattr mult_2_net.v```
  
  In case of the file called mult_8.v, the steps are the same.
 

## Day 3: Combiantional and Secuential optimization

**The key for an optimize a digitial circuit is focus on Area and Power saving**
We will se two techniques for optimization on digital circuits:
- Cosntant propagation(direct optimization)
- Boolean logic optimization (using k-map for example)
## Constant propagation optimization
Consider the circuit of the next figure. And remember that every logic gate has a propagation delay. So, it's possible design a circuit that the ouput behavioral will be equivalent to the another circuit but whit less propagation delay. The answer to this question is yes, and that is the key for this type of optimization.
In the case of the last figuere, this circuit is equivalent to a neg-buffer. See the next figure.
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/7.jpg?raw=true)
**Note that the number of transistor for implement the circuit of the last figure will be different in both cases. A less number of transistors is equivalent to have a circuit that consume less power and use less area! **

## Boolean optimization
Consider the next boolean expresion * assign y = a?(b?c:(c?a:0)):(!c). Using boolean algebra is possible to reduce this original expresion. The reduction of this expresion is equivalent to obtain a new circuit (simplified) that will be optimized in terms of area and power consumption because the epresion of this new circuit is a simplification of the original expresion. See the next figure:
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/8.jpg?raw=true)

## Cloning and Retiming optimization
Cloning optimization consist in cloning one part of the circuit that is far away of the other parts. See the next figure
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/9.jpg?raw=true)

Retiming optimization consist in modificate some part of the logic of one cicuit for reduce the max propagation delay, and increment the max frecuency of the clock.
In the next figure, Logic1 and Logic2 has a propagation delay equal to 5ns and 2ns respectively, so the max clock frecuency is 200MHz.
Now we can modify part of the Logic1 and also modify part of the Logic2 and compensate the slow stage with the fast stage, and in consequence increment the max clk frecuency. See the next figure
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/10.jpg?raw=true)
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/11.jpg?raw=true)

# Lab: combinational logic optimization 

consider the next verilog code  ```opt_check.v ```
 ```verilog
module opt_check (input a , input b , output y);
        assign y = a?b:0;
endmodule
 ```
On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog opt_check.v
  $ synth -top opt_check
  ```
  Optimize, run:
  ```opt_clean -purge```
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show opt_check```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/9.jpg?raw=true)

consier the another verilog code called  ```opt_check2.v

 ```verilog
module opt_check2 (input a , input b , output y);
        assign y = a?1:b;
endmodule
 ```
On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog opt_check2.v
  $ synth -top opt_check2
  ```
  Optimize, run:
  ```opt_clean -purge```
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show opt_check2```
  ![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/diagrams/10.jpg?raw=true)
  
# Lab Sequential Logic Optimisations

Consider the next verilog code:

 ```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
        if(reset)
                q <= 1'b0;
        else
                q <= 1'b1;
end

endmodule ```

On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_const1.v
  $ synth -top dff_const1
  ```
  Now we need to force the tool yosys for use only DFF, so run:
  ``` $ dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_const1```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/11.jpg?raw=true)

 ```verilog
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
        if(reset)
                q <= 1'b1;
        else
                q <= 1'b1;
end

endmodule ```

On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_const2.v
  $ synth -top dff_const2
  ```
  dfflib it's was included in the last module so is not necessary to import now.
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_const2```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/12.jpg?raw=true)

 ```verilog
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
        if(reset)
        begin
                q <= 1'b1;
                q1 <= 1'b0;
        end
        else
        begin
                q1 <= 1'b1;
                q <= q1;
        end
end

endmodule ```

On yosys we can synthesized this file. So, run:

 ``` $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  $ read_verilog dff_const3.v
  $ synth -top dff_const3
  ```
  dfflib it's was included in the last module so is not necessary to import now.
  
  Create the netlist file, run:
  ``` abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
  To see a graphical representation of the netlist using the command view. So, run:
  ``` $ show dff_const3```
![alt text](https://github.com/HALxmont/RTL_design_workshop/blob/main/DAY3/13.jpg?raw=true)



## Lab: Seq optimisation unused outputs part1

