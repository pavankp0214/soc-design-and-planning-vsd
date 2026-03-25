
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

## Day 2: Good Floorplan vs. Bad Floorplan and Introduction to Library Cells

### Floorplanning
The floorplanning phase is critical for the physical design flow. It involves:
* Defining the **Core and Die area**.
* Setting the **Aspect Ratio** and **Utilization Factor**.
* Placing **I/O Pins** along the perimeter.
* Placing **Decoupling Capacitors** (Decap cells) and pre-placed macros.
* Generating the **Power Distribution Network (PDN)** (VDD and VSS power straps).

To run the floorplan in OpenLANE, use the following command:
```tcl
run_floorplan
```
Successful Power Distribution Network (PDN) Generation:
![PDN Generation](images/Day_2_FP_Comp.png)

Floorplan Layout in Magic:
Notice the rows defined for standard cells and the I/O pins placed evenly around the edges.![Floorplan Layout](images/Day_2_FP.png)

I/O Pin Placement Analysis:
Using the what command in the Magic tkcon console, we can inspect the specific metal layers used for our I/O pins.
![Vertical Pins](images/Day_2_V_ML.png)
![Horizontal Pins](images/Day_2_H_ML.png)

Placement
Once the floorplan is set, the tool moves on to Placement. This occurs in two stages:

Global Placement: The tool places standard cells to minimize wire length, ignoring overlaps.
![Global Placement](images/Day_2_placement.png)

Detailed Placement: The tool legalizes the cells by ensuring they fit exactly into the standard cell rows without any overlaps.


To run placement in OpenLANE:

```tcl
run_placement
```
Global Placement View:

Detailed Placement View (Zoomed In):
Here we can see the individual standard cells strictly aligned to the power and ground rails.
![Detailed Placement](images/Day_2_detailed_view.png)

## Day 3: Design and Characterization of One Library Cell

### Part 1: Custom CMOS Inverter Design and SPICE Simulation
Standard cells are the fundamental building blocks of digital physical design. In this section, we characterize a custom CMOS inverter by extracting its parasitic values and simulating it to find its propagation delays and transition times.

**Cloning the Custom Inverter Repository:**
We start by cloning the `vsdstdcelldesign` repository, which contains the Magic layout of a custom CMOS inverter, and opening it using the Magic layout tool.

**Inverter Layout in Magic:**

**Identifying the NMOS and PMOS regions:**
Using the `what` command in the tkcon window, we can identify the specific mask layers used to build the PMOS and NMOS transistors.

**SPICE Extraction from Magic:**
To simulate the behavior of this layout, we must extract its parasitic resistance and capacitance into a SPICE netlist. We do this using the `extract all` and `ext2spice` commands in the tkcon window.

**Modifying the SPICE Deck:**
The extracted SPICE netlist (`sky130_inv.spice`) needs to be modified to include the correct standard cell library models, power supply voltages (VDD = 3.3V, VSS = 0V), input pulse parameters, and the `.tran` command for transient analysis.

**Running ngspice Simulation:**
With the SPICE deck configured, we simulate the circuit using `ngspice` to plot the output voltage (`y`) against the input voltage (`a`) over time.

**Transient Analysis Waveform:**

**Characterizing the Cell Timing:**
To use this custom cell in our OpenLANE flow, we need to characterize its timing parameters from the graph coordinates. We calculate four main values:
1. **Rise Time:** Time taken for the output to rise from 20% to 80% of its maximum value.
2. **Fall Time:** Time taken for the output to fall from 80% to 20% of its maximum value.
3. **Cell Rise Delay:** Difference in time between the input falling to 50% and the output rising to 50%.
4. **Cell Fall Delay:** Difference in time between the input rising to 50% and the output falling to 50%.

*(You can enter your calculated values from your terminal here!)*
