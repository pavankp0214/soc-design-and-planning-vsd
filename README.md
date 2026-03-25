
## Day 1: Inception of Open-source EDA, OpenLANE, and Sky130 PDK

### RTL to GDSII Flow
The RTL to GDSII flow is the process of translating a logical Register Transfer Level (RTL) design into a physical layout (GDSII) ready for fabrication. OpenLANE is an automated RTL to GDSII flow built around open-source tools like Yosys, OpenROAD, Magic, and OpenSTA. 

### Invoking OpenLANE
The first step is to invoke the OpenLANE docker container and start the interactive flow.

![Invoking OpenLANE](images/openlane_docker.png)

### Running Synthesis
Logic synthesis translates the RTL code into a gate-level netlist using standard cells from the Sky130 PDK. 

To run synthesis, the following commands are used:
```tcl
prep -design picorv32a
run_synthesis
```

### Flop Ratio Calculation
After synthesis, it is crucial to analyze the design statistics to understand the hardware footprint. From the yosys synthesis reports, we can calculate the flop ratio.

![Total Cells](images/Day_1_no_of_cells.png)
![D-Flip Flops](images/Day_1_D_FF.png)

* **Total Number of Cells:** 14876
* **Number of D-Flip-Flops:** 1613
* **Flop Ratio Calculation:** `(Number of D-Flip-Flops / Total Number of Cells)`
* **Flop Ratio:** 1613 / 14876 = **0.1084** (or **10.84%**)
