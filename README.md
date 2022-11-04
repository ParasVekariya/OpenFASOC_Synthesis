# OpenFASOC_Floorplan

- [Tools used and Installation](#tools-used-and-installation)
- [Deriving RTL-to-GDS Flow](#deriving-rtl-to-gds-flow)
  * [Input - Verilog File](#input---verilog-file)
  * [Working](#working)
  * [Output](#output)


The FASoC Program aims to create a complete system-on-chip (SoC) synthesis tool, from user specification to GDSII. FASoC uses a unique technology to automatically synthesise "correct-by-construction" Verilog descriptions for both analogue and digital circuits, allowing for a portable, single pass implementation flow. Analog circuits, such as PLLs, power management, ADCs, and sensor interfaces, are realised using the SoC synthesis tool by recasting them as structures composed primarily of digital components while maintaining analogue performance. They are then expressed as synthesizable Verilog blocks made up of digital standard cells supplemented by a few auxiliary cells generated by an automated cell generation tool. We then enable the composition of a large number of digital and analogue components into a single correct-by-construction design component by expanding the IPXACT format and the ARM Socrates tool.

## Tools used and Installation

Installation guide can be found on [readthedocs](https://openfasoc.readthedocs.io/en/latest/getting-started.html#installation) website.
Tools used are as follows:
1. OpenRoad
2. Magic
3. Yosys
4. Klayout

It is recommended to download and avail all the dependencies required for the project as mentioned above for smooth flow of project.

## Deriving RTL-to-GDS Flow

### Input - Verilog File

![BlackBox](https://user-images.githubusercontent.com/81183082/199916951-ce4a4478-6178-40de-8841-ab5c8f29b087.jpeg)

Using python script present in the **tools** directory which reads a .json file mentioning the specifications required and after verification of the parameters, we get the verilog file which has circuit specification as mentioned in the json file.

### Working

![Workflow](https://user-images.githubusercontent.com/81183082/199916972-7711a8dd-70b6-4bb6-9590-1cb484393c98.jpeg)

Considering the working of RTL-GDS as a black box system, we get the inputs in the form of verilog file which gets feeded in the black box and then we get the output as a compiled verilog version in the pysical circuit mode. We will focus mainly on this black box system.

OpenRoad takes the input as the desgin file which is present in the design directory of the project where a **config.mk** and **verilog** files are present (inputs from previous state) along with that it also choses a platform (sky130hd in here) which are spcifiying the design specifications. We can also keep tracks of individual steps as OpenRoad creates the files in the /flow/results folder where each file will be numbered as per the step.

After this yosys runs for the synthesis and choses appropriate circuit from the available cells.

**Floorplan**

![tempsensegenDirectory](https://user-images.githubusercontent.com/81183082/199917028-d4f8a427-7ee6-4451-a918-710a8cde6d7a.jpeg)

Floorplan requires information about the power network with voltage domains and power rails which is required to know the power layout so that the circuit works properly and get a working power range for the circuit.

 ``` pdn.tcl ``` (previously pdn.cfg) provides all information on power layout required such as die area, core area and site coordinates. For example temperature sensor generator works on two power rails  such as **VDD** which powers most of the circuit and **Vin** for the ring oscillator which is present through the Header cells that we used in auxilliary cells. These voltage domains (specific to the citcuit) can be provided using the floorplan.tcl script file.

### Output
After this final step is when the layout is actually deployed using the scirpt file ```read_domain_instances.tcl ``` which reads the config.mk file and assigns the instances to the domains mentioned and also makes sure that the cells are placed in the correct voltage domains.
 
 Now the floorplan is ready and is passed to the next stage of Placement.
