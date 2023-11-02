# pes_decoder

# TABLE OF CONTENTS
- [Installing Software](#installing-software)
- [Functional Simulation](#functional-simulation)
- [Synthesis](#synthesis)
- [Gate Level Simulation](#gate-level-simulation)
  

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

![image](https://github.com/benedict04/pes_decoder/assets/109859485/58917a50-802c-4e08-b651-567bee5e2bd1)



# PHYSICAL DESIGN

- Physical Design flow is a cardinal process of converting synthesized netlist, design curtailment and standard library to a layout as per the design rules provided by the foundry. This layout is further sent to the foundry for the creation of the chip.
- Synthesis translates RTL designs written in VHDL or Verilog HDL into gate-level specifications that can be understood by the next set of tools. The cells employed, their interconnections, the area used, and other parameters are all listed in this netlist.
- Under floorplanning, we calculate the dimensions of all the blocks and place them in appropriate spots on the chip. This step is performed to keep the blocks that are highly connected close to one another.
- Placement is the process of placing the standard cells inside the core boundary in an optimal location. The tool tries to place the standard cell in such a way that the design should have minimal congestions and the best timing.Placement does not place only the standard cells present in the synthesized netlist but also places many physical only cells and adds buffers/inverters as per the requirement to meet the timings, DRV, and foundry requirements.
- Clock Tree Synthesis(CTS) is one of the crucial steps in VLSI physical design flow. It is used to reduce skew and insertion delay. This step helps distribute the clock evenly among all sequential elements of a design.
- Routing helps in making the links between the cells and the blocks. There are two types of routing: global routing and detailed routing. Connections are routed through global routing, which assigns routing resources. It also keeps track of a network’s assignment. Whereas, the actual connections are made by detailed routing.


## OpenLane
OpenLANE is an opensource tool or flow used for opensource tape-outs. The OpenLANE flow comprises a variety of tools such as Yosys, ABC, OpenSTA, Fault, OpenROAD app, Netgen and Magic which are used to harden chips and macros, i.e. generate final GDSII from the design RTL. The primary goal of OpenLANE is to produce clean GDSII with no human intervention. OpenLANE has been tuned to function for the Google-Skywater130 Opensource Process Design Kit.

#### Software setup

refer below link for OpenLANE installation:

https://openlane.readthedocs.io/en/latest/

### Making Config file for running OpenLane
```
"DESIGN_NAME": "pes_decoder",
    "VERILOG_FILES": "/home/hardhik/OpenLane/openlane/pes_decoder/src/pes_decoder.v",
    "DIE_AREA": "0.0 0.0 500 500",
    "FP_PDN_VPITCH": 25,
    "FP_PDN_HPITCH": 25,
    "FP_PDN_VOFFSET": 5,
    "FP_PDN_HOFFSET": 5,
    "PL_TARGET_DENSITY": 0.95,
    "FP_SIZING": "absolute",
    "SYNTH_AUTONAME": 1,
    "GLB_RESIZER_MAX_WIRE_LENGTH": 6000,
    "PL_RESIZER_MAX_WIRE_LENGTH": 6000,
    "PL_BASIC_PLACEMENET": 0,
    "CLOCK_PORT": "clk",
    "CLOCK_PERIOD": 12.0
```
In order to overcome succesfull placement we need to configure our own values for various variables based on the design.



### Automate the whole ASIC flow
Now we go into the OpenLane folder and run the following command to automate the whole ASIC flow.
```
sudo make mount
./flow.tcl -design openlane/pes_brg -tag RUN
```
