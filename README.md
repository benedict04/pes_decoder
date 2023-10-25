# pes_decoder

# TABLE OF CONTENTS
- [Introduction](#introduction)
- [Working](#working)
- [Functional Simulation](#functional-simulation)
- [Synthesis](#synthesis)
- [Gate Level Simulation](#gate-level-simulation)
- [Physical Design](#physical-design)

### Installing Software 
```
sudo apt-get install git 
sudo apt-get install iverilog 
sudo apt-get install gtkwave 
```

### Executing the project
```
cd vsd/sky130RTLDesignAndSynthesisWorkshop
iverilog pes_decoder.v pes_decoder_tb.v 
gtkwave dec.vcd
```

![image](https://github.com/benedict04/pes_decoder/assets/109859485/55304e6e-f590-4554-8a58-756b9618d1c4)

![image](https://github.com/benedict04/pes_decoder/assets/109859485/ad927cc4-4025-417b-8f79-87e99806686f)

## Synthesis 
The software used to run gate level simulation is yosys.
### Yosys
**Yosys** is a framework for RTL synthesis tools. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains.
Yosys can be adapted to perform any synthesis job by combining the existing passes (algorithms) using synthesis scripts and adding additional passes as needed by extending the yosys C++ code base.
### Installing Yosys
1. Download yosys.sh from the repo
2. Open terminal and go to the directory where yosys.sh is present
3. run the commands `chmod +x yosys.sh` `./yosys.sh`
### Steps to Synthesis
- Go to pes_brg directory
- once you get to pes_brg directory, Invoke yosys by using the command yosys
- once yosys is invoked follow the above sequence of commands
  ``` sh
  read_liberty -lib lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog pes_decoder.v
  synth -top pes_decoder
  abc -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  flatten
  write_verilog -noattr pes_decoder_net.v
  show
  ```


![image](https://github.com/benedict04/pes_decoder/assets/109859485/e83bdaf4-3667-4af9-a3af-4c00f49a5ff9)

## Gate Level Simulation
GLS stands for Gate level Simulation. when we make an RTL design we test it with the help of test bench, which applies some stimulus. The output of that stimulus is checked for the desired functionality. After synthesis we need to again check if the functionality is maintained, for that we perform the GLS. We put the netlist under test and use the same test bench that we did for RTL design to check the functionality. GLS also ensures the timing of the design.




![image](https://github.com/benedict04/pes_decoder/assets/109859485/5da45068-84f6-4d2a-8387-216170d2e71e)


```
iverilog ../my_lib/verilog_Model/primitives.v ../my_lib/verilog_Model/sky130_fd_sc_hd.v pes_decoder_net.v pes_decoder_tb.v
./a.out
gtkwave dec.vcd
```

![image](https://github.com/benedict04/pes_decoder/assets/109859485/d6a00e80-dcc0-4034-94d6-5d1561ff5da0)

![image](https://github.com/benedict04/pes_decoder/assets/109859485/ca8e2c51-e1cf-4e8e-941c-7304c45f460a)
