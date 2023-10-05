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


