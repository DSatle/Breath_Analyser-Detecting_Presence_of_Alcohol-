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
int main()
{
	int sensor;//bit 0
	int buzzer;//bit 1
	int clk_freq = 10000000;
	int shift=0xFFFFFFF2;
	
	buzzer = 0;
	
	asm volatile(
	"and x30, x30, %1\n\t"
	"or x30, x30, %0\n\t"
	:
	:"r"(buzzer),"r"(shift)
	:"x30"
	);
	for(int i=0;i<300;i++);// random delay to initialize MQ3 sensor
	
	while(1)
	{
		asm volatile(
		"addi x10, x30, 0\n\t"
		"andi %0, x10, 1\n\t"
		:"=r"(sensor)
		:
		:"x10"
		); 
		
		if(sensor==1)//Alcohol detected buzzer on
		{
			buzzer = 1;
			
			asm volatile(
			"and x30, x30, %1\n\t"
		    	"or x30, x30, %0\n\t"
		    	:
			:"r"(buzzer),"r"(shift)
			:"x30"
			);
			for(int i=0;i<100;i++);
		}
		else//Alcohol not detected buzzer off
		{
			buzzer = 0;
			
			asm volatile(
			"and x30, x30, %1\n\t"
		    	"or x30, x30, %0\n\t"
		    	:
			:"r"(buzzer),"r"(shift)
			:"x30"
			);
			for(int i=0;i<100;i++);
		}
	}
	return(0);
}
```


# Assembly level Program

```
c.out:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fd010113          	addi	sp,sp,-48
   10058:	02812623          	sw	s0,44(sp)
   1005c:	03010413          	addi	s0,sp,48
   10060:	009897b7          	lui	a5,0x989
   10064:	68078793          	addi	a5,a5,1664 # 989680 <__global_pointer$+0x977d4c>
   10068:	fef42023          	sw	a5,-32(s0)
   1006c:	ff200793          	li	a5,-14
   10070:	fcf42e23          	sw	a5,-36(s0)
   10074:	fc042c23          	sw	zero,-40(s0)
   10078:	fd842783          	lw	a5,-40(s0)
   1007c:	fdc42703          	lw	a4,-36(s0)
   10080:	00ef7f33          	and	t5,t5,a4
   10084:	00ff6f33          	or	t5,t5,a5
   10088:	fe042623          	sw	zero,-20(s0)
   1008c:	0100006f          	j	1009c <main+0x48>
   10090:	fec42783          	lw	a5,-20(s0)
   10094:	00178793          	addi	a5,a5,1
   10098:	fef42623          	sw	a5,-20(s0)
   1009c:	fec42703          	lw	a4,-20(s0)
   100a0:	12b00793          	li	a5,299
   100a4:	fee7d6e3          	bge	a5,a4,10090 <main+0x3c>
   100a8:	000f0513          	mv	a0,t5
   100ac:	00157793          	andi	a5,a0,1
   100b0:	fcf42a23          	sw	a5,-44(s0)
   100b4:	fd442703          	lw	a4,-44(s0)
   100b8:	00100793          	li	a5,1
   100bc:	04f71063          	bne	a4,a5,100fc <main+0xa8>
   100c0:	00100793          	li	a5,1
   100c4:	fcf42c23          	sw	a5,-40(s0)
   100c8:	fd842783          	lw	a5,-40(s0)
   100cc:	fdc42703          	lw	a4,-36(s0)
   100d0:	00ef7f33          	and	t5,t5,a4
   100d4:	00ff6f33          	or	t5,t5,a5
   100d8:	fe042423          	sw	zero,-24(s0)
   100dc:	0100006f          	j	100ec <main+0x98>
   100e0:	fe842783          	lw	a5,-24(s0)
   100e4:	00178793          	addi	a5,a5,1
   100e8:	fef42423          	sw	a5,-24(s0)
   100ec:	fe842703          	lw	a4,-24(s0)
   100f0:	06300793          	li	a5,99
   100f4:	fee7d6e3          	bge	a5,a4,100e0 <main+0x8c>
   100f8:	fb1ff06f          	j	100a8 <main+0x54>
   100fc:	fc042c23          	sw	zero,-40(s0)
   10100:	fd842783          	lw	a5,-40(s0)
   10104:	fdc42703          	lw	a4,-36(s0)
   10108:	00ef7f33          	and	t5,t5,a4
   1010c:	00ff6f33          	or	t5,t5,a5
   10110:	fe042223          	sw	zero,-28(s0)
   10114:	0100006f          	j	10124 <main+0xd0>
   10118:	fe442783          	lw	a5,-28(s0)
   1011c:	00178793          	addi	a5,a5,1
   10120:	fef42223          	sw	a5,-28(s0)
   10124:	fe442703          	lw	a4,-28(s0)
   10128:	06300793          	li	a5,99
   1012c:	fee7d6e3          	bge	a5,a4,10118 <main+0xc4>
   10130:	f79ff06f          	j	100a8 <main+0x54>
```
# Distinctive assembly code instructions

```
Number of different instructions: 11
List of unique instructions:
add
sw
lui
li
lw
and
or
j
bge
mv
bne

```
![distinct code](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/834f4034-60d2-4c05-9660-928feb210f6a)

# Output of the C program is shown below

![output trail](https://github.com/DSatle/Breath_Analyser-Detecting_Presence_of_Alcohol-/assets/140998466/29d77cfc-01af-460d-bc74-e443109d0dc8)

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








