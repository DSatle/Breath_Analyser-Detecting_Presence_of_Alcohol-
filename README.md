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
	
	asm(
	"and x30, x30, %1\n\t"
	"or x30, x30, %0\n\t"
	:"=r"(buzzer)
	:"r"(shift));
	for(int i=0;i<300000000;i++);// ranom delay to initialize MQ sensor
	
	while(1)
	{
		asm(
		"addi x10, x30, 0\n\t"
		"and %0, x10, 1\n\t"
		:"=r"(sensor)); 
		
		if(sensor==1)//presence of alcohol detected buzzer on
		{
			buzzer = 1;
			
			asm(
			"and x30, x30, %1\n\t"
		    	"or x30, x30, %0\n\t"
		    	:"=r"(buzzer)
			:"r"(shift));
			for(int i=0;i<1000000;i++);
		}
		else//presence of alcohol not detected buzzer off
		{
			buzzer = 0;
			
			asm(
			"and x30, x30, %1\n\t"
		    	"or x30, x30, %0\n\t"
		    	:"=r"(buzzer)
			:"r"(shift));
			for(int i=0;i<1000000;i++);
		}
	}
}

```


# Assembly level Program

```
output.o:     file format elf32-littleriscv


Disassembly of section .text:

00000000 <main>:
   0:	fd010113          	add	sp,sp,-48
   4:	02812623          	sw	s0,44(sp)
   8:	03010413          	add	s0,sp,48
   c:	009897b7          	lui	a5,0x989
  10:	68078793          	add	a5,a5,1664 # 989680 <.L8+0x9895a8>
  14:	fef42023          	sw	a5,-32(s0)
  18:	ff200793          	li	a5,-14
  1c:	fcf42e23          	sw	a5,-36(s0)
  20:	fc042c23          	sw	zero,-40(s0)
  24:	fdc42783          	lw	a5,-36(s0)
  28:	00ff7f33          	and	t5,t5,a5
  2c:	00ff6f33          	or	t5,t5,a5
  30:	fcf42c23          	sw	a5,-40(s0)
  34:	fe042623          	sw	zero,-20(s0)
  38:	0100006f          	j	48 <.L2>

0000003c <.L3>:
  3c:	fec42783          	lw	a5,-20(s0)
  40:	00178793          	add	a5,a5,1
  44:	fef42623          	sw	a5,-20(s0)

00000048 <.L2>:
  48:	fec42703          	lw	a4,-20(s0)
  4c:	11e1a7b7          	lui	a5,0x11e1a
  50:	2ff78793          	add	a5,a5,767 # 11e1a2ff <.L8+0x11e1a227>
  54:	fee7d4e3          	bge	a5,a4,3c <.L3>

00000058 <.L10>:
  58:	000f0513          	mv	a0,t5
  5c:	00157793          	and	a5,a0,1
  60:	fcf42a23          	sw	a5,-44(s0)
  64:	fd442703          	lw	a4,-44(s0)
  68:	00100793          	li	a5,1
  6c:	04f71263          	bne	a4,a5,b0 <.L4>
  70:	00100793          	li	a5,1
  74:	fcf42c23          	sw	a5,-40(s0)
  78:	fdc42783          	lw	a5,-36(s0)
  7c:	00ff7f33          	and	t5,t5,a5
  80:	00ff6f33          	or	t5,t5,a5
  84:	fcf42c23          	sw	a5,-40(s0)
  88:	fe042423          	sw	zero,-24(s0)
  8c:	0100006f          	j	9c <.L5>

00000090 <.L6>:
  90:	fe842783          	lw	a5,-24(s0)
  94:	00178793          	add	a5,a5,1
  98:	fef42423          	sw	a5,-24(s0)

0000009c <.L5>:
  9c:	fe842703          	lw	a4,-24(s0)
  a0:	000f47b7          	lui	a5,0xf4
  a4:	23f78793          	add	a5,a5,575 # f423f <.L8+0xf4167>
  a8:	fee7d4e3          	bge	a5,a4,90 <.L6>
  ac:	fadff06f          	j	58 <.L10>

000000b0 <.L4>:
  b0:	fc042c23          	sw	zero,-40(s0)
  b4:	fdc42783          	lw	a5,-36(s0)
  b8:	00ff7f33          	and	t5,t5,a5
  bc:	00ff6f33          	or	t5,t5,a5
  c0:	fcf42c23          	sw	a5,-40(s0)
  c4:	fe042223          	sw	zero,-28(s0)
  c8:	0100006f          	j	d8 <.L8>

000000cc <.L9>:
  cc:	fe442783          	lw	a5,-28(s0)
  d0:	00178793          	add	a5,a5,1
  d4:	fef42223          	sw	a5,-28(s0)

000000d8 <.L8>:
  d8:	fe442703          	lw	a4,-28(s0)
  dc:	000f47b7          	lui	a5,0xf4
  e0:	23f78793          	add	a5,a5,575 # f423f <.L8+0xf4167>
  e4:	fee7d4e3          	bge	a5,a4,cc <.L9>
  e8:	f71ff06f          	j	58 <.L10>

```


