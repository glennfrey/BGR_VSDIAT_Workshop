# Bandgap Reference
![App Screenshot](/public/images/VSD_IAT.jpeg)
# Bandgap Voltage Reference using sky130nm PDK

Bandgap reference circuit

![App Screenshot](/public/images/Bandgap_ref.jpeg)
It is a temperature dependent voltage reference circuit.

It produce constant voltage regardless of  variation in power supply,temperature ,and loadin circuit.

Challenges: 

1. A battery is unsuitable for use as a reference voltage source as voltage drop after a period of  time.

2. A Voltage reference IC used buried Zener Diode:discrete design require extra components and filtering circuit for frequency to extract thermal noise. 
Low voltage zener diode not available

3. Power supply is not suitable:it provide noisy output.


Solution:

Band Gap reference can be integrated in bulk using MOSFET,BJT.

Applications of BGR

1. Analog to Digital converter

2. Digitalto Analog converter

3. DC to Dc bulk converter

4. Low Dropout regulator

Basic Principal of BGR

It is the combination of CTAT to PTAT voltage generator 

![App Screenshot](/public/images/Bandgap_ref_Intro.jpeg)

Types of BGR

Architechture wise :
1. Using self- bias corrent mirror,
2. Using Op-amp


Application wise : 
1. Low voltage BGR,
2. High -PSRRand low noise ,
3. Low power,
4. curvature compensated


Advantages :Easy to design,Simple topology,stable.

Limitation : Low power supply rejection ratio, Startup circuit required, Cascode design is needed to reduce the PSRR

Construction OF BGR:
Components
1. CTAT voltage generation circuit :(complementry to absolute temperature)

![App Screenshot](/public/images/CTAT.jpeg)

The CTAT Voltage generation circuit consist of a BJT connected as a diode

![App Screenshot](/public/images/CTAT_diff_topo.jpeg)

a. if no. of PNP transistor =1

b. if no. of parallel connected PNP Transistor =8

c. if variable current source is applied


2. PTAT Voltage generation circuit 

The PTAT Voltgae generation circuit consist of N BJTs connected with a series resistance.

3. Startup circuit (NA)

4. Reference branch circuit 


## Tools and PDK setup

the design and simulation of the BGR circuit is performed using following Tools
Spice netlist simulation - Ngspice

## Design and Pre-layout Simulation

Design Requirements
Color             | Hex                                                                |
| ----------------- | ------------------------------------------------------------------ |
| Supply voltage | 2V|
| Temperature | -40 to 125 Deg Cent. |
|  Power Consumption | < 60uW |
|Tempco. Of Vref| <50ppm|

     
Device Data Sheet

1. MOSFET

Parameter  	    NFET 	/                            PFET

Type 	         LVT 	                            

Voltage     	1.8V 	                            

Vt0 	        ~0.4V 	                            ~-0.6V

Model 	:    sky130_fd_pr__nfet_01v8_lvt 	/sky130_fd_pr__pfet_01v8_lvt

2. BJT (PNP)

Parameter :	PNP

Current Rating 	:1uA-10uA/um2

Beta 	:~12

Vt0 	:11.56 um2

Model :	sky130_fd_pr__pnp_05v5_W3p40L3p40


3. RESISTOR (RPOLYH)

Parameter 	RPOLYH

Sheet Resistance 	~350 Ohm

Tempco. 	2.5 Ohm/Deg Cent

Bin Width 	0.35u, 0.69u, 1.41u, 5.37u

Model 	sky130_fd_pr__res_high_po


## BGR Design
Here we are showing simulation of CTAT and BGR

CTAT Simulation

1. CTAT Voltage generation with single BJT
![App Screenshot](/public/images/ctat_bjt_sp.jpeg)
![App Screenshot](/public/images/ctat_graph.png)

2. CTAT Voltage generation with multiple BJT

## Screenshots

![App Screenshot](/public/images/ctat_mult_bjt_sp.jpeg)
![App Screenshot](/public/images/ctat_multi_bjt_graph.png)

3. CTAT Voltage generation with different current source values

## Screenshots

![App Screenshot](/public/images/ctat_i_var_sp.jpeg)
![App Screenshot](/public/images/ctat_i_var.png)

![App Screenshot](/public/images/ctat_I_VARI_GRAPH.jpeg)


BGR Simulation

CTAT voltage +PTAT voltage = Vref

![App Screenshot](/public/images/BGR_CKT_IDEAL_VCVS.jpeg)
![App Screenshot](/public/images/BGR_ideal_sp2.jpeg)
![App Screenshot](/public/images/Bgrdiff_equ.jpeg)
![App Screenshot](/public/images/bgr_VREF_VQ3.jpeg)

## Conclusion
We can find that the Vref = Vctat+Vptat



