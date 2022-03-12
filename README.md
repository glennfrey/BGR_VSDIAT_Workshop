# Bandgap Voltage Reference using sky130nm PDK
![](bgr_workshops.png)
 - [Day 1: Bandgap Design Theory](#Day1)
  * [Introduction to BGR](#Introduction-to-BGR)
  * [BGR Principle](#BGR-Principle)
  * [CTAT Voltage generation circuit](#CTAT-Voltage-generation-circuit)
  * [PTAT Voltage generation circuit](#PTAT-Voltage-generation-circuit)
  * [Self-Biased Current Mirror Circuit](#SelfBiased-Current-Mirror-Circuit)
  * [Reference Branch Circuit](#Reference-Branch-Circuit)
  * [Start-up circuit](#Startup-circuit)
  * [Complete BGR Circuit](#Complete-BGR-Circuit)
  * [Tools Used](#Tools-Used)
  * [Specification](#Specification)
- [Day 2: BGR Labs and post-layout simulations](#Day2)
  * [Pre-Layout Simulation](#PreLayout-Simulation)
  * [CTAT Simulation](#CTAT-Simulation)
  * [PTAT Simulation](#PTAT-Simulation)
  * [BGR using Ideal Opamp](#BGR-using-Ideal-Opamp)
  * [BGR with SBCM](#BGR-with-SBCM)
  * [Layout Design](#Layout-Design)
  * [Block Design](#Block-Design)
  * [Top level design](#Top-level-design)
  * [Author](#Author)
  * [Acknowledgement](#Acknowledgement)


## Day1
Bandgap reference circuit

## Introduction to BGR
The Bandgap Reference is a circuit which generates stable voltage which is independent of factors like temperature, supply voltage. A battery cannot be used because it's voltage will drop after a time period. And to avoid noisy voltages and additional components we use bandgap reference which can be integrated in bulk-CMOS, bi-CMOS without external components.
![](bgr_workshop/BGR1.png)
## BGR Principle
The principle of BGR circuits is to sum a voltage with negative temprature coefficient with another one exhibiting opposite temperature dependancies. In general, semiconductor diodes act as CTAT, or complement to absolute temperature, which implies that as temperature rises, the voltage across the diode decreases. As a result, we must devise a PTAT circuit capable of cancelling out the CTAT nature, i.e. with an increase in temperature. The voltage across that device will rise, allowing us to get a constant voltage reference with regard to temperature.
![](bgr_workshop/BGR_Principle.png)
## CTAT Voltage generation circuit
CTAT behaviour is common in semiconductor diodes. When a constant current flows through a forward biassed diode, we can see that the voltage across the diode decreases as the temperature rises. In general, the slope of the VTemp is determined to be -2mV/deg C. The CTAT Voltage generating circuit is made up of a BJT linked as a diode, which demonstrates the CTAT nature described above.
![](bgr_workshop/CTAT.png)
![](bgr_workshop/CTAT1.png)
![](bgr_workshop/Equation.png)
## PTAT Voltage generation circuit
Diode current equation has two parts, i.e. Vt (Thermal Voltage) which is directly proportional to the temp and Is (Reverse saturation current) which is directly proportional to the temp.

So, in order to create a PTAT Voltage generating circuit, we must first figure out how to extract Vt from Is. The circuit should consist of N BJTs linked with a series resistance to isolate Vt from Is.
![](bgr_workshop/PTAT.png)
![](bgr_workshop/PTAT1.png)
![](bgr_workshop/PTATCKT.png)
![](bgr_workshop/PTATEQN.png)
## SelfBiased Current Mirror Circuit
The self-biased current mirror is a sort of current mirror that does not need to be biased externally. Without any external current source reference, this current mirror biases itself to the desired current value.
![](bgr_workshop/currentmirror.png)
## Reference Branch Circuit
The final reference voltage is obtained by adding the CTAT and PTAT voltages in the reference circuit branch. In the reference branch, we use a mirror transitor and a BJT as a diode. The reference branch receives the same amount of current as the current mirror branches due to the mirror transistor. We now have PTAT voltage and PTAT current coming from the PTAT circuit branch. In the reference branch, the same PTAT current is flowing. However, the PTAT voltage slope is substantially smaller than the CTAT voltage slope. We must raise the resistance in order to enhance the voltage slope. We now have our constant reference voltage, which is the consequence of CTAT Voltage + PTAT Voltage across the high resistance.
![](bgr_workshop/refbranch1.png)
## Startup circuit
To transfer the self biassed current mirror from the degenerative bias point, the start-up circuit is necessary (zero current). When the current in the current mirror branches is zero, the start-up circuit forcibly flows a small amount of current through the self-biased current mirror. Because the current mirror is self-biased, this small current causes a disturbance, and the current mirror auto-biased to the desired current value.
![](bgr_workshop/startup.png)
## Complete BGR Circuit
By connecting the subcircuits we get the total circuit,
![](bgr_workshop/fullbgr.png)
## Tools Used
Ngspice: for simulation of spice files
Magic: for creating layouts for circuits
Netgen: tool for comparing netlists
## Specification
Supply voltage = 1.8V
Temperature: -40 to 125 Deg Cent.
Power Consumption < 60uW
Off current < 2uA
Start-up time < 2us
Tempco. Of Vref < 50 ppm
## Day2
To simulate these sub circuits, we are going to use netlist and simulate it using ngspice. To create a netlist, first create a file with a filename.sp extension. Use the first line as a comment to describe about the circuit for easier identification. First to create a valid spice netlist, we have to include a library with its path and its corner name.
![](bgr_workshop/layout/github.png)
![](bgr_workshop/layout/github_eda_technology.png)
```
.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
```
And if we going to use a specific model, we have to include that in the netlist ( in this case, we are using sky130_fd_pr__pnp_05v5_W3p40L3p40 model)
```
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"
```
The syntax which we are going to use for independent voltage/current sources is,
```
Vxx n1 n2 dc 1.8 : *Vxx* - Voltage source, *n1* - Node-1 of voltage source, *n2* - Node-2 of voltage source, *dc* - Type (can be dc/ac) *1.8* - value
Ixx n1 n2 dc 10u : *Ixx* - Current source, *n1* - Node-1 of current source, *n2* - Node-2 of current source, *dc* - Type (can be dc/ac) *1.8* - value
```
For simulating the design in DC the syntax is,
```
.dc temp -40 125 5 : Simulate for temp varying from -40 to 125 with 5 dec cent step
.dc Vs1 0 1.8 0.01 : Simulate for Vs1 varying from 0V to 1.8V with 0.01V step
```
![](bgr_workshop/layout/spice_directory_sourcefile.png)
## CTAT Simulation
## CTAT Voltage generation with single BJT
The aim of this simulation is to find the voltage variation across BJT with respect to the temp if use current source of 10uA. The spice netlist which we are going to simulate is,
```
**** ctat voltage generation circuit *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"

.global vdd gnd
.temp 27

*** bjt definition
xqp1	gnd	gnd	qp1	gnd	sky130_fd_pr__pnp_05v5_W3p40L3p40	m=1

*** supply voltage and current
vsup	vdd	gnd	dc	2
isup	vdd	qp1	dc 	10u
.dc	temp	-40	125	5

*** control statement
.control
run
plot v(qp1)
.endc
.end
```
And to simulate this netlist, type ngspice ctat_voltage_gen.sp. Now we the see the corresponding waveform after the simulation
![](bgr_workshop/prelayout/ctat@2v.png)
## CTAT Voltage genration with multiple BJTs
In this simulation, we are going to see calculate voltage across 8 BJTs which are parllely connectd. The spice netlist which we are going to simulate is,
```
**** ctat voltage generation circuit with multiple bjt *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"

.global vdd gnd
.temp 27

*** bjt definition
xqp1	gnd	gnd	qp1	gnd	sky130_fd_pr__pnp_05v5_W3p40L3p40	m=8

*** supply voltage and current
vsup	vdd	gnd	dc	2
isup	vdd	qp1	dc 	10u
.dc	temp	-40	125	5

*** control statement
.control
run
plot v(qp1)
.endc
.end
```
And to simulate this netlist, type ngspice ctat_voltage_gen_mul_bjt.sp. Now we the see the corresponding waveform after the simulation
![](bgr_workshop/prelayout/ctat_mul_bjt.png)
## CTAT Voltage generation with different current source values
In this simulation, the main objective is to check the CTAT voltage dependency on current. The spice netlist which we are going to simulate is,
```
**** ctat voltage generation circuit with variable current *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"


.global vdd gnd
.temp 27

*** bjt definition
xqp1	gnd	gnd	qp1	gnd	sky130_fd_pr__pnp_05v5_W3p40L3p40	m=1

*** supply current
vsup	vdd	gnd	dc	2
isup	vdd	qp1	dc 	10u
.dc	temp	-40	125	5	isup	1.25u	10u	1.25u

*** control statement
.control
run
plot v(qp1)
.endc
.end
```
And to simulate this netlist, type ngspice ctat_voltage_gen_var_current.sp. Now we the see the corresponding waveform after the simulation
![](bgr_workshop/prelayout/ctat_cur.png)
## PTAT Simulation
## PTAT Voltage generation with ideal current source
In this simulation, the main objective is to find the voltage difference between the two terminals of the resistance. The spice netlist which we are going to simulate is,
```
**** ptat voltage generation circuit *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"

.global vdd gnd
.temp 27

	
*** bjt definition
xqp2    gnd     gnd     qp2     vdd     sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8

*** supply voltage and current
isup	vdd	ra1	dc	10u
vsup	vdd	gnd	dc 	2
.dc	temp	-40	125	1

*** control statement
.control
run
plot v(ra1) v(qp2) v(ra1)-v(qp2)
.endc
.end
```
And to simulate this netlist, type ngspice ptat_voltage_gen_ideal_current_source.sp. Now we the see the corresponding waveform after the simulation
![](bgr_workshop/prelayout/ptat_cir.png)
## PTAT Voltage generation with VCVS
In this simulation, the main objective is to check the amplified PTAT voltage using one VCVS. The spice netlist which we are going to simulate is,
```
**** ptat voltage generation circuit *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"

.global vdd gnd
.temp 27

*** vcvs definition
e1	net2	gnd	ra1	qp1	gain=1000
xmp1    q1	net2   	vdd	vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp2    q2    	net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
	
*** bjt definition
xqp1	gnd	gnd	qp1	vdd	sky130_fd_pr__pnp_05v5_W3p40L3p40	m=1
xqp2    gnd     gnd     qp2     vdd     sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8

*** voltage sources for current measurement
vid1	q1	qp1	dc	0
vid2	q2	ra1	dc	0

*** supply voltage
vsup	vdd	gnd	dc 	2
.dc	temp	-40	125	5

*** control statement
.control
run
plot v(qp1) v(ra1) v(qp2) v(net2)
plot vid1#branch vid2#branch
.endc
.end
```
And to simulate this netlist, type ngspice ptat_voltage_gen.sp. Now we the see the corresponding waveform after the simulation
![](bgr_workshop/prelayout/ptat_vcvs.png)
## Resistance tempco.
The voltage across the resistor also increases with increase in the temperature, which makes the resistor similar to PTAT. The main objective of this simulation is to check the tempco. of resistor using ideal current source of 10uA. The spice netlist which we are going to simulate is,
```
**** ctat voltage generation circuit *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"

.global vdd gnd
.temp 27

*** resistor definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

*** supply current
vsup	vdd	gnd	dc	2
vid     qp2     gnd     dc      0
isup	gnd	ra1	dc 	10u
.dc	temp	-40	125	1	

*** control statement
.control
run
plot v(ra1) 
plot v(ra1)/vid#branch
.endc
.end
```
And to simulate this netlist, type ngspice res_tempco.sp. Now we the see the corresponding waveform after the simulation
![](bgr_workshop/prelayout/res_tempco.png)
To find the PTAT voltages across the resistance for different current values from the following curve we have to simulate the netlist res_tempco_var_current.sp
```
**** RES tempco circuit *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"

.global vdd gnd
.temp 27

*** resistor definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

*** supply current
vsup	vdd	gnd	dc	2
vid     qp2     gnd     dc      0
isup	gnd	ra1	dc 	10u
.dc	temp	-40	125	1	isup	1.25u	10u	1.25u

*** control statement
.control
run
plot v(ra1) 
plot v(ra1)/vid#branch
.endc
.end
```
![](bgr_workshop/prelayout/res_tempco_var_i.png)
## BGR using Ideal Opamp
To simulate the BGR behaviour using one VCVS as an ideal OpAmp, we have to simulate the netlist bgr_using_ideal_opamp.sp
```
**** bgr using ideal opamp (vcvs) *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"

.global vdd gnd
.temp 27

*** vcvs definition
e1 net2 gnd ra1 qp1 gain=1000


xmp1    qp1	net2   	vdd	vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp2    ra1 	net2   	vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmp3    ref	net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
	
*** bjt definition
xqp1	gnd	gnd	qp1	gnd	sky130_fd_pr__pnp_05v5_W3p40L3p40	m=1
xqp2    gnd     gnd     qp2     gnd     sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8
xqp3    gnd     gnd     qp3     gnd     sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1

*** high-poly resistance definition
xra1    ra1     na1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xra2    na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8

xrb1    ref     nb1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41	l=7.8
xrb2    nb1     nb2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb3    nb2     nb3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb4    nb3     nb4     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb5    nb4     nb5     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb6    nb5     nb6     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb7    nb6     nb7     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb8    nb7     nb8     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb9    nb8     nb9     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb10   nb9     nb10    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb11   nb10    nb11    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb12   nb11    nb12    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb13   nb12    nb13    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb14   nb13    nb14    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb15   nb14    nb15    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb16   nb15    nb16    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41 	l=7.8
xrb17   nb16    nb17    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41   	l=7.8
xrb18   nb17    nb18    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41   	l=7.8
xrb19   nb18    nb19    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41   	l=7.8
xrb20   nb19    nb20    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	 l=7.8
xrb21   nb20    nb21    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41   	l=7.8
xrb22   nb21    nb22    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	 l=7.8
xrb23   nb22    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8
xrb24   nb22    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  	l=7.8

*** voltage source for current measurement

*** supply voltage
vsup    vdd     gnd     dc      2
*.dc    vsup    0       3.3     0.3.3

.dc	temp    -40     125     5

*vsup    vdd     gnd     pulse   0       2       10n     1u      1u      1m      100u
*.tran   5n      10u

.control
RUN
plot v(vdd) v(qp1) v(ra1) v(qp2) v(ref) v(qp3)
plot v(ref)


.endc
.end
```
![](bgr_workshop/prelayout/ideal_bgr.png)
## BGR with SBCM
Let's replace the ideal Op-Amp with self-biased current mirror which is our proposed design and observe. The expected output is of that which in case of ideal OpAmp based BGR. Now lets check Behaviour of the circuit in TT corner using the spice netlist bgr_lvt_rpolyh_3p40.sp
```
**** bandgap reference circuit using self-biase current mirror *****

.lib "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130.lib.spice tt"
.include "/home/ramachandra14519/Desktop/ramachandra_bgr/eda-technology/sky130/models/spice/models/sky130_fd_pr__model__pnp.model.spice"

.global vdd gnd
.temp 27

*** circuit definition ***

*** mosfet definitions self-biased current mirror and output branch
xmp1	net1	net2	vdd	vdd	sky130_fd_pr__pfet_01v8_lvt	l=2	w=5	m=4
xmp2    net2    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5    	m=4
xmp3    net3    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=4
xmn1    net1    net1    q1      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8
xmn2    net2    net1    q2      gnd     sky130_fd_pr__nfet_01v8_lvt     l=1     w=5     m=8

*** start-upcircuit
xmp4    net4    net2    vdd     vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp5    net5    net2    net4    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=1
xmp6    net7    net6    net2    vdd     sky130_fd_pr__pfet_01v8_lvt     l=2     w=5     m=2
xmn3    net6    net6    net8    gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1
xmn4    net8    net8    gnd     gnd     sky130_fd_pr__nfet_01v8_lvt     l=7     w=1     m=1

*** bjt definition
xqp1	gnd	gnd	qp1	vdd	sky130_fd_pr__pnp_05v5_W3p40L3p40	m=1
xqp2    gnd     gnd     qp2     vdd     sky130_fd_pr__pnp_05v5_W3p40L3p40       m=8
xqp3    gnd     gnd     qp3     vdd     sky130_fd_pr__pnp_05v5_W3p40L3p40       m=1

*** high-poly resistance definition
xra1	ra1	na1	vdd	sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra2	na1     na2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra3    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xra4    na2     qp2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

xrb1    vref    nb1     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb2    nb1     nb2     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb3    nb2     nb3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb4    nb3     nb4     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb5    nb4     nb5     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb6    nb5     nb6     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb7    nb6     nb7     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb8    nb7     nb8     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb9    nb8     nb9     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb10   nb9     nb10    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb11   nb10    nb11    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb12   nb11    nb12    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb13   nb12    nb13    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb14   nb13    nb14    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb15   nb14    nb15    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb16   nb15    nb16    vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb17   nb16    qp3   	vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8
xrb18   nb16    qp3     vdd     sky130_fd_pr__res_high_po_1p41     w=1.41  l=7.8

*** voltage source for current measurement
vid1	q1	qp1	dc	0
vid2    q2      ra1     dc      0
vid3    net3    vref    dc      0
vid4    net7	net1	dc	0
vid5	net5	net6	dc	0

*** supply voltage
vsup	vdd	gnd	dc 	2
*.dc	vsup	0	3.3	0.3.3
.dc	temp	-40	125	5

*vsup	vdd	gnd	pulse	0	2	10n	1u	1u	1m	100u
*.tran	5n	10u

.control
run

plot v(vref)

.endc
.end
```
![](bgr_workshop/prelayout/bgr_tt.png)
## Layout Design
The Layout Design for the sub circuits are done magic tool. The invoke the magic tool for new design, the command to type is,
```
magic -T <technology_file_name.tech> <layout.mag>
```
## Basic Cell Design
## Design of NFET
In our design we have used two different size nfets:

W=5 L=1
![](bgr_workshop/layout/nfet_mag2.png)
![](bgr_workshop/layout/nfet1_mag.png)
## Design of PFET
W=5 L=2
![](bgr_workshop/layout/pfet_mag.png)
## Design of Resistor
poly resistors of W=1.41 L=7.8
![](bgr_workshop/layout/res1p41_mag.png)
## Design of PNP (BJT)
![](bgr_workshop/layout/images/pnpt1.png)

## Block Design
## Design of NFETs
![](bgr_workshop/layout/nfets_mag2.png)
## Design of PFETs
![](bgr_workshop/layout/pfets_mag2.png)
## Design of RESBANK
![](bgr_workshop/layout/resbank_mag2.png)
## Design of PNP10
![](bgr_workshop/layout/pnp10_mag.png)
## Design of STARTERNFET
![](bgr_workshop/layout/startemfet_mag.png)
## Top level design
![](bgr_workshop/layout/top_mag2.png)

## Author
Glenn Frey Olamit, self

## Acknowledgement
Kunal Ghosh, VSD Corp
Santunu Sarangi, Silicon Institute of Technology (SIT), Bhubaneswar




