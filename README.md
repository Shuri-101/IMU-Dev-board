# IMU STM32STM32G431CBT6 DEV BOARD:

<img width="1037" height="761" alt="image" src="https://github.com/user-attachments/assets/5dc3786a-16a6-46f5-b547-0cbe8b512bd7" />
<img width="698" height="628" alt="image" src="https://github.com/user-attachments/assets/2ce3f879-b770-49e4-913f-12a6519b35eb" />

Hello, this is just a project for me to get some experience and learn about data busses like SPI (Serial Peripheral Interface), UART (Universal Asynchronous Receiver/Transmitter) and I2C (Inter-Integrated Circuit) and learn how to program sensors like IMUs, barometers, altimeters, GPS etc. This because I hope to design a flight computer for a drone or active stabilized rocket so yeah. 

## PCB
### Schematic:
<img width="1348" height="771" alt="image" src="https://github.com/user-attachments/assets/ae363694-50df-48bf-be56-1d93841886f1" />

Since I had 48pins on my MCU I thought might as well use them and connect them to headers so I can use them later to connect other sensors and stuff.

Note for beginners: Decoupling capacitors are meant to smooth out power spikes and dips that might cause logic errors in the MCU, they also act as filtering capacitors keeping noise off sensitive rails like analogue (noise is unwanted voltage that you did not put there) as analogue data is the measure of voltage. 

Example of my decoupling capacitors are: 

<img width="755" height="437" alt="image" src="https://github.com/user-attachments/assets/9fbf7df3-684f-4bae-bf2b-b4c6ad8abcd9" />

Note that the capacitors are placed in parallel to the 3V3 line. 4.7uF is a bulk capacitors meant to refill the small capacitors between the spikes and dips that get drained very quickly during the spikes and dips. 

For more info about decoupling watch: 
https://www.youtube.com/watch?v=u40kX1DYKdA

### Why I chose these parts?

- I chosen the STM32STM32G431CBTX since it offered FPU, CRC and a mathematical accelerator which are needed to control these sensors, I chosen the LQFP-48 package since its easier to solder and it was the only variant available to buy at the time.
- FPU, a floating point unit allows you to work with floating point numbers such as a fraction, decimals etc 100x faster than a MCU without a FPU, since sensors output raw numbers that need to converted into data requires floating point maths hundreds of times per second.
- DSP, Digital Signal Processing lets you do fast repeated multiply-and-add on a stream of numbers, which is needed to do maths for the sensors roughly twice as fast than a MCU without it.

- I chose the LSM6DS3TR-C as its cheap has 6 axis and a +/- 2000 degrees / second and it has +/- 16g which works for learning how to program and design a PCB for an IMU. 

### Layers

#### Layer 1 (Main signal routing layer)
- In this layer I routed most of my signals + decupling / filtering capcitors (since they need to be placed as close to the thing its meant to decoupling / filter around 5mm max) taking care to have proper clearance from aggressive signals such as SCK to avoid crosstalk and interference from other signals.
- I also used local copper pours to create a low impedance route to GND.
- LDO is placed far away from IMU at the opposite end of the board to prevent the heat it generates from interfering with the readings.

<img width="715" height="603" alt="image" src="https://github.com/user-attachments/assets/b34c70b4-3c92-490d-8540-d1c15924f514" />

#### Layer 2 (Ground Plane):
- This layer is a layer of copper connected ground aka "ground plane" and offers a low impedance path (simple terms path of low total resistance) to GND and also acts as a reference plane for signal return currents.
- Notice I have not routed anything on this layer, this is to maintain a unbroken reference plane otherwise signal return currents will have to loop around interfering with other signals.
- Using a ground plane also makes way way way easier to route your GND traces instead of using long traces back to power supply.
  
<img width="673" height="588" alt="image" src="https://github.com/user-attachments/assets/b4017d06-dacf-4cdf-a0e6-4904f6ef0e65" />

#### Layer 3 (3V3 Power Plane):
- This layer is another layer of copper but this time its connected to 3V3 from my LOW Dropout Converter which converts 5V --> 3.3V cause MCUs and my IMU runs on 3.3v logic.
- The 3v3 plane makes it easier to route my power supplies to my components that need it as its saves room as you can see from the picture I just use vias to route up from the plane to the MCU.
- Here you can see I routed signals here because I had no other choice as this board is quite small, this inevitably caused islands of copper to form on the PCB meaning I had to create localised copper pours on layer 3 to stitch these islands togther.

<img width="691" height="597" alt="image" src="https://github.com/user-attachments/assets/de25c002-2f5e-4175-92da-cf550230d4fc" />

#### Layer 4 (3V3 stitching and Signal routing):
- In this layer I also used it to route signals I could not route on other layers as I had no room.
- I also used this layer to stitch the 3V3 plane up.
- The signals on this layer references with the 3V3 power plane because decoupling capacitors tie 3V3 and GND together.

<img width="741" height="602" alt="image" src="https://github.com/user-attachments/assets/a5d99206-2960-4d9f-8076-c1d9bfa2ee0c" />

## BOM:
#### Parts:
USING LCSC:
- C967633: IMU
<img width="1302" height="232" alt="image" src="https://github.com/user-attachments/assets/3a1da705-aee2-41ca-8534-c4c7c4d8e790" />
- C529355: MCU
<img width="1312" height="205" alt="image" src="https://github.com/user-attachments/assets/3f1b9302-7cfc-4cb6-872c-fee6d1ec8d20" />
- C6186: LDO
<img width="1288" height="212" alt="image" src="https://github.com/user-attachments/assets/f333f5cd-bcd9-46ae-88d5-427cb9fd8f7f" />
- C20608785: YELLOW LED
<img width="1280" height="190" alt="image" src="https://github.com/user-attachments/assets/7e376679-7d2e-4698-be3a-3bbef3b9f384" />
- C2894893: USB-C PORT
<img width="1266" height="181" alt="image" src="https://github.com/user-attachments/assets/c03c06ff-6189-4853-932e-2a25d3ed0af0" />
- C19171391: RED LED
<img width="1268" height="197" alt="image" src="https://github.com/user-attachments/assets/681a56bf-1a95-42b7-b909-33165607b966" />
- C33546008: 22uF MLCC XR5 (Filtering)
<img width="1292" height="192" alt="image" src="https://github.com/user-attachments/assets/31fb84d5-eb92-4f1f-af69-e0bcb9abe108" />
- C36911367: 100nF MLCC XR7 (Decoupling)
<img width="1286" height="172" alt="image" src="https://github.com/user-attachments/assets/35cbfbad-10de-4431-98ee-bba83f33394a" />
- C107365: 4.7uF MLCC XR7 (Bulk decoupling)
<img width="1267" height="197" alt="image" src="https://github.com/user-attachments/assets/f1ea56c3-b349-4e4f-a060-bd927f7e510a" />
- C53282227: 1uF MLCC XR7
<img width="1278" height="170" alt="image" src="https://github.com/user-attachments/assets/4072dd7f-5468-4560-8ec7-55aac66b7517" />
- C519909: 10nF MLCC XR7
<img width="1267" height="197" alt="image" src="https://github.com/user-attachments/assets/1df28d6e-7f4b-4492-939b-c8bf29ea8ce6" />
- C118848: 5.1K Resistor
<img width="1311" height="200" alt="image" src="https://github.com/user-attachments/assets/b68442fb-931a-42d1-a039-b78b633ce8e8" />
- C54921199: 470 Resistor
<img width="1303" height="192" alt="image" src="https://github.com/user-attachments/assets/f9460036-903f-4a82-8694-f21ac1c2d157" />
- C54921203: 4.7K Resistor
<img width="1293" height="190" alt="image" src="https://github.com/user-attachments/assets/99e79212-36e5-43e8-864a-97d154547be4" />
- C54921094: 10K Resistor
<img width="1272" height="187" alt="image" src="https://github.com/user-attachments/assets/a07f7b06-26f7-456f-a7b1-da682020f616" />

#### Estimated total:
<img width="445" height="606" alt="image" src="https://github.com/user-attachments/assets/ee174ebe-67a4-49f1-8130-643fde1215f1" />




















## Firmware:
Since I don't know how to code in C and as I mentioned at the start this board is meant to get me to learn how to code, ill just send the config file for the IMU pins ONLY since I dont know what other things I want to plug into the board yet, and that is decide by the end user, but I'll set up the main configs for the MCU like clock SWD(for programming) and pins for the IMU. Also will need to learn fully about clocks, how they work, and how to configure data lines in CUBE MX...



