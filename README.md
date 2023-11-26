# Breath_Analyser-Detecting_Presence_of_Alcohol

# Objective

The objective of an alcohol detector is to accurately and reliably measure the presence of alcohol in a person's breath, blood, or other bodily fluids to promote safety and responsible behavior. This device aims to:

* Prevent Impaired Driving
* Ensure Workplace Safety
* Enhance Public Health
# Functioning of Breath Analyser

The Breath Analyser consists of the MQ3 sensor. Paired with the RISC-V processor. MQ3 sensor module has a built in LM393 precision comparator which converts the anlog signal to digital output for the RISC-V. The module includes a potentiometer for adjusting the sensitivity of the digital output (D0). You can use it to set a threshold so that when the alcohol concentration exceeds the threshold value, the module outputs LOW otherwise HIGH.

**Rotating the knob clockwise increases sensitivity and counterclockwise decreases it.**

**Setting the threshold**

The module has a built-in potentiometer for setting an alcohol concentration threshold above which the module outputs LOW and the status LED lights up.

![Screenshot 2023-10-05 150645](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/a79b8b12-68da-4c2e-b6e9-f489d1b6aef4)

Now, to set the threshold, let the alcohol vapors enter the sensor and turn the pot clockwise until the Status LED is on. Then, turn the pot back counterclockwise just until the LED goes off.

That’s all there is to it; your module is now ready to use.

The block diagram of the breath analyser is given below.

![block diagram](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/dfaae820-f835-43d1-bda9-d868ebf83106)

# C program of the Breathe Analyser

```
// Function to analyse whether the given person is drunk or not
//#include<stdio.h>
int main()  {

    int sensor_status;
    int buzzer;
    int masking;
    int i;
    int sensor_state;
    int Alcohol0,Alcohol1;
    
    
        //for (int j=0; j<15;j++) {
        while(1){
        
	/*
       if(j<10)
			sensor_status=1;
	else
			sensor_status=0;
		*/	

		asm volatile(
		"or x30, x30, %1\n\t"
		"andi %0, x30, 0x01\n\t"
		: "=r" (sensor_state)
		: "r" (sensor_status)
		: "x30"
		);
	

 
       
        if (sensor_status == 0) {
            // If alcohol is below the permissable limit, set the buzzer control register to 0 (buzzer off)
            masking=0xFFFFFFFD;
          //  printf("No alohol detected \n");
          buzzer = 0; 
       
            asm volatile(
            "and x30,x30, %0\n\t"     // Load immediate 1 into x30
            "ori x30, x30,2"                 // output at 2nd bit , keeps buzzer in off
            :
            :"r"(masking)
            :"x30"
            );
            asm volatile(
	    	"addi %0, x30, 0\n\t"
	    	:"=r"(Alcohol0)
	    	:
	    	:"x30"
	    	);
    	//printf("Alcohol0 = %d\n",Alcohol0);
            
      
        } 
        else {
            // If Alcohol is above the permissible limit, set the buzzer control register to 1 (buzzer on)
            masking=0xFFFFFFFD;
             buzzer = 1; 
          //  printf("Alcohol presence detected \n ");
            asm volatile( 
            "and x30,x30, %0\n\t"     // Load immediate 1 into x30
            "ori x30, x30,0"            //// output at 2nd bit , switches on the buzzer
            :
            :"r"(masking)
            :"x30"
        );
        asm volatile(
	    	"addi %0, x30, 0\n\t"
	    	:"=r"(Alcohol1)
	    	:
	    	:"x30"
	    	);
	 //printf("Alcohol1 = %d\n",Alcohol1);
        }

 // printf("buzzer=%d \n", buzzer);   

}

return 0;
}

```
# Assembly level conversion
```
riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -ffreestanding -nostdlib -o ./out Alcohol.c
riscv64-unknown-elf-objdump -d  -r out > Alcohol_Detector_assembly.txt
```
# Assembly level Program

```
out:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fd010113          	addi	sp,sp,-48
   10058:	02812623          	sw	s0,44(sp)
   1005c:	03010413          	addi	s0,sp,48
   10060:	fec42783          	lw	a5,-20(s0)
   10064:	00ff6f33          	or	t5,t5,a5
   10068:	001f7793          	andi	a5,t5,1
   1006c:	fef42423          	sw	a5,-24(s0)
   10070:	fec42783          	lw	a5,-20(s0)
   10074:	02079463          	bnez	a5,1009c <main+0x48>
   10078:	ffd00793          	li	a5,-3
   1007c:	fef42223          	sw	a5,-28(s0)
   10080:	fe042023          	sw	zero,-32(s0)
   10084:	fe442783          	lw	a5,-28(s0)
   10088:	00ff7f33          	and	t5,t5,a5
   1008c:	002f6f13          	ori	t5,t5,2
   10090:	000f0793          	mv	a5,t5
   10094:	fcf42e23          	sw	a5,-36(s0)
   10098:	fc9ff06f          	j	10060 <main+0xc>
   1009c:	ffd00793          	li	a5,-3
   100a0:	fef42223          	sw	a5,-28(s0)
   100a4:	00100793          	li	a5,1
   100a8:	fef42023          	sw	a5,-32(s0)
   100ac:	fe442783          	lw	a5,-28(s0)
   100b0:	00ff7f33          	and	t5,t5,a5
   100b4:	000f6f13          	ori	t5,t5,0
   100b8:	000f0793          	mv	a5,t5
   100bc:	fcf42c23          	sw	a5,-40(s0)
   100c0:	fa1ff06f          	j	10060 <main+0xc>
```
# Distinctive assembly code instructions

```
Number of different instructions: 11
List of unique instructions:
sw
andi
li
addi
and
j
mv
or
bnez
ori

```
![instruction code](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/190cb44e-4f23-4d02-9189-45ab451cb632)


# Output of the C program is shown below

![output trail](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/29d77cfc-01af-460d-bc74-e443109d0dc8)


# Spike Simulation Results
The output of the spike is shown below where sensor value =1, indicates Presence of Alcohol is detected and buzzer is turned on i.e buzzer =1, contrary to that if presence of alcohol is not detect buzzer is off, the image shows output as 2 whose binary equivalent is 10. 

![spike_output](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/9c5ab018-9db1-4046-a99f-8b92c70a4639)

# Functional Simulation's Conclusive Data

The below image shows the change in output with respect to change in input. Low value of input indicates that the alcohol consumption is under permissable limmit  and buzzer is off i.e output is also low, contrary to that when the person has drunk above permissible limit output of the sensor which is input to the processor is high and buzzer is turned on.

![Screenshot from 2023-10-27 16-48-03](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/e13d6654-412e-464d-9d82-c737463a2206)

Toggle in input is reflected in output as well.

The below image shows clock of the system, top gpio pins, Instruction ID.

![Screenshot from 2023-10-27 18-19-43](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/9a96bf9b-9483-4d8e-bbbd-ea495873a750)

The below image shows increament in Instruction ID, which implies that execution of instructions from the program counter.

![Screenshot from 2023-10-31 11-56-10](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/b8411d25-7cd0-4eb2-a863-84b1bacf1c59)

The below image shows that with change in the instruction ID, when input changes output value is changed corresponding to that.

![Screenshot from 2023-10-31 11-55-51](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/6ecde109-1713-4e62-9448-5c156b0d6e35)

The below image having two markers marked showing the delay for output change with respect to change in input.

![Screenshot from 2023-10-31 11-55-51](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/901e58f1-b2b9-4392-ad2c-b1310217ce5a)

| Input value with time stamp| Output Value with time stamp | Delay|
| :---                       |     :---:                    | ---: |
| 1(4500Sec)                 |  1(4530Sec)                  |30 Sec|
| 0(5000Sec)                 | 0(5050Sec)                   | 50Sec|

The input toggling is given for 500units in the testbench code, the same is reflected in the waveform.

![Screenshot from 2023-10-31 12-12-23](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/40b88b1e-c7c2-431e-92d3-a4d01f7d6d20)

The below image shows that input value is read at the instruction and corresponding value dumping in the output.


| Input Instruction ID & Value  | Output Instruction ID & Value |
| ------------- 		| ------------- |
| 1 (FEF42423)  		| 1(02079463)  |

![Screenshot from 2023-10-31 12-38-37](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/d6c2dd43-5e66-4f7c-abc1-847055b09477)

## Gate level Simulation

To perform the gate level simulation following commands were used

```
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80_256.lib 
read_verilog processor.v 
synth -top wrapper
dfflibmap -liberty sky130_fd_sc_hd__tt_025C_1v80_256.lib 
abc -liberty sky130_fd_sc_hd__tt_025C_1v80_256.lib
write_verilog Synth_test.v
```
To run the GLS command following commands were used 

```
iverilog -o test synth_processor_test.v testbench.v sky130_sram_1kbyte_1rw1r_32x256_8.v sky130_fd_sc_hd.v primitives.v
```


The same results i.e. output following input is replicated in the Gate level Simulation with instruction ID and delay.

The first image shows the output following input.

![1](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/bd26a9f4-0ee3-4cb1-8fa9-8d213a26df13)

The below image includes instruction ID, write done signals as well.

![2](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/05fc8713-9bf7-4059-996f-2aa4e7c4f87c)

![3](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/bd30ebe5-b59a-417b-b8f2-77c400ac9459)

The above two images shows a 20 sec delay which processor takes to ammend the output change with respect to input change, first output is rising after input rises, second output falls after input falls.
# Netlist Generation
To generate the netlist following commands were used 
```
show wrapper
```
Below image shows the netlist generated with highlighted wrapper module.

![Screenshot from 2023-10-31 23-30-11](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/8b24a151-d99a-4d07-bfd2-efa151980253)

# OpenLane

To run Openlane following commands were used in the terminal.

```
make mount
./flow.tcl -interactive
package require openlane 0.9
prep -design project
```
![flow_interactive](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/9012fbf6-0558-445f-a161-da83a903f4cf)

# Config.json

![Screenshot from 2023-11-16 21-01-05](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/e7980598-9462-4c4a-b186-396dbbd810c8)


# Synthesis

Synthesis is the process of transfering higher level of abstraction(RTL) to implementable lower level of abstraction. It is the process of transforming RTL to gate-level netlist.

Synthesis = Translation + Logic Optimisation + Mapping

```
run_synthesis
```

![Screenshot from 2023-11-15 16-18-24](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/7559baaa-cd94-4bb2-b933-30eb381dda08)

chip area after synthesis is shown below

![Screenshot from 2023-11-16 21-07-00](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/23250351-2603-4c2c-a3de-af86dc69bfd2)


# Floorplan 

Floor planning is the first step in the physical design flow. Floor Planning is a quintessential step which decides the layout of the VLSI design. A good floorplan can be make implementation process (place, cts, route & timing closure) cake walk.

![Screenshot from 2023-11-16 22-15-27](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/9d5fda13-1a50-4b1c-b799-4991cc851223)

```
run_floorplan
```
![Screenshot from 2023-11-16 21-11-27](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/176c52c1-4f20-435e-b0aa-3a2799b5d9eb)

Die Area details are shown below

![Screenshot from 2023-11-16 21-15-58](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/f5b8c943-b6ba-456c-bbdc-adca7d2bfe93)

Core Area details

![Screenshot from 2023-11-16 21-16-05](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/7160a307-ee39-436b-9673-43a9f89d168d)


# Placement 

* Placement is the process of placing standard cell in the design, the tool determines the location of each standard cell on the die, the tool places these on basis of algorithm which it uses internally.

* Placement does not just place the standard cells available in the synthesized netlist, it also optimizes the design, placement also determines the routability of the design.

* Placement will be driven by the different criteria like timing driven, congestion driven and power optimisation.

```
run_placement
```
 ![Screenshot from 2023-11-15 16-32-28](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/f062dea8-aad3-4dac-9b81-bb9b1a25b9fc)

 # Clock Tree Synthesis
 
 Clock Tree Synthesis is a technique for distributing the clock equally among all sequential parts of a VLSI design. The purpose of Clock Tree Synthesis is to reduce skew and delay.
 
 ```
run_cts
```
Timing report is shown below

![Screenshot from 2023-11-16 21-21-42](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/aec69a4a-2c8c-4bda-ab79-dc8bfbfaaa8f)

![path_type_placement](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/577f0f87-ca7b-4817-9751-068b0d87b512)

![area_cts](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/de872ee8-5029-40cf-b359-f6427e29eb9c)

![skew_cts](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/d0aebce1-ad53-4d1c-9c65-fab2e31de3f1)

Power report is shown below

![Screenshot from 2023-11-16 21-29-50](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/21bc9a5e-7b3d-41c5-9835-363b83040a2e)

# Routing 

* Routing is the stage after CTS, routing is nothing but connecting the various blocks with one another.
* Routing creates physical connections to all clock and the signal pins through metal interconnects
* After CTS, we have information about the exact location of placed cells, blockages, clock tree buffers/invertes I/O pins. The tool depends on this information to electrically complete all connections
* In routing stage, metal and visas are used to create the electrical connection in layout, to complete all connetions defined by the netlist.
```
run_routing
```
![Screenshot from 2023-11-16 22-27-09](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/5f1ae80f-a069-4375-bec9-a80985899198)

![Screenshot from 2023-11-16 22-30-16](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/c034c994-88e6-45f1-9a81-537396775d6b)

![Screenshot from 2023-11-16 22-29-40](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/7fd29673-2a10-419b-b0b4-723ce2ebc843)

Area Report

![Screenshot from 2023-11-16 22-57-05](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/5ee52a9b-f7d9-4768-9fb1-922cf7fff8ac)

![Screenshot from 2023-11-16 22-38-38](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/7835ebff-14a5-44e7-8ddd-7660830fd8c6)


# Word of thanks

I want to extend my appreciation to Kunal Ghosh(Founder & CEO VSD) for affording me the opportunity to learn about the ASIC design flow through his provision of high-quality educational materials.

I would also like to express my gratitude to Mayank Kabra(Founder, Chipcron Pvt. Ltd.) for his invaluable contributions and insights during the project's completion.

# References

* https://lastminuteengineers.com/mq3-alcohol-sensor-arduino-tutorial/
* https://github.com/SakethGajawada/RISCV-GNU
* Pruthvi Parate, Colleague, IIIT Bangalore
* Bhargav DV, Colleague, IIIT Bangalore
* Koparthi Dinesh, IIIT Bangalore








