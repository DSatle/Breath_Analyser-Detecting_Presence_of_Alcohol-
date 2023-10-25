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

riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -ffreestanding -nostdlib -o ./out Alcohol.c
riscv64-unknown-elf-objdump -d  -r out > Alcohol_Detector_assembly.txt


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
Number of different instructions: 12
List of unique instructions:
mv
and
addi
li
bge
andi
or
j
bge
lw
bne
lui

```

![new_diff_int](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/ba223697-e781-4a62-a5f4-4402e340e357)

# Output of the C program is shown below

![output trail](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/29d77cfc-01af-460d-bc74-e443109d0dc8)


# Spike Simulation Results
The output of the spike is shown below where sensor value =1, indicates Presence of Alcohol is detected and buzzer is turned on i.e buzzer =1, contrary to that if presence of alcohol is not detect buzzer is off, the image shows output as 2 whose binary equivalent is 10. 

![spike_output](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/9c5ab018-9db1-4046-a99f-8b92c70a4639)

# Functional Simulation's Conclusive Data

In the image below, it's evident that the ID_instructions' content is incrementing in a manner that mirrors the progression of the assembly code, aligning with the PC's execution increment

![3](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/d35501eb-488f-4644-a3f8-b7f69d190f2a)

The image provided below demonstrates that when the "write done" signal is in a low state, the operation remains unexecuted since the instruction isn't fetched. As a result, any input variation does not affect the output, and it remains constant.

![2](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/5b49e79d-1468-4324-a9bc-8f91b2a04444)

The image reveals a variation in the output corresponding to changes in the input. This is attributed to the "write done" signal being in a high state, indicating that an instruction has been successfully fetched, and as a result, the operation is executed.
When the input is in a high state, it signifies that the alcohol level surpasses the allowable limit, causing an increase in the output signal and subsequently turning on the buzzer.

![1](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/ab849e74-53a4-431e-a3c5-dcc413741b79)

# Word of thanks

I want to extend my appreciation to Kunal Ghosh(Founder & CEO VSD) for affording me the opportunity to learn about the ASIC design flow through his provision of high-quality educational materials.

I would also like to express my gratitude to Mayank Kabra for his invaluable contributions and insights during the project's completion.

# References

* https://lastminuteengineers.com/mq3-alcohol-sensor-arduino-tutorial/
* https://github.com/SakethGajawada/RISCV-GNU
* Pruthvi Parate, Colleague, IIIT Bangalore
* Bhargav DV, Colleague, IIIT Bangalore
* Niharika Vulchi, IIIT Bangalore








