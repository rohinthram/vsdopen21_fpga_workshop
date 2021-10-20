# VSDOpen21 FPGA Workshop

![Final Lab]()

![VSDOpen21](/assets/vsdopentutorial_banner.png)

This is the report for the one day workshop on "Digital Design using `virtual` FPGA"


# Contents
 - [Platform Used](#Platform-Used)
 - [Introduction](#Introduction)
 - [Interfacing LED](#Interfacing-LED)
 - [Problem Statement](#Problem-Statement)
 - [Problem Solution](#Problem-Solution)
 - [Report By](#Report-By)
 - [Acknowledgements](#Acknowledgements)



# Platform Used
 - Makerchip (https://makerchip.com/)

    Makerchip is a web based platform for Virtual FPGA Simulations

    More at: https://github.com/BalaDhinesh/Virtual-FPGA-Lab


# Introduction
- Makerchip platform supports 5 different FPGAs (as of now), they are    
    * 1 - Zedboard
    * 2 - Artix-7
    * 3 - Basys3
    * 4 - Icebreaker
    * 5 - Nexys

To select the borad and use, 
```
m4_define(M4_BOARD, <board_no>);
```
replace `<board_no>` with number provided above to use respective board

After this the board is initialized using 
```
m4+fpga_init();
```

- Interfacing LED
Makerchip instance to inteface LED
```
m4+fpga_led(*led);
```
where *led will be the register to store state of LED(on -1 , off - 0)

- Interfacing Seven Segment Display
Makerchip instance to inteface LED
```
m4+fpga_sseg(*digit, *segment, *dp);
```
- digit is to *enable a seven segment display(digit)* (register length depends on digit availabel)
- segment is to *enable the segment of the digit enabled* (7 bit register)
- dp is to *enable the decimal point* 

# Interfacing LED
Verilog code
```
reg [7:0] led = 8'b10010101;
```

![LED at makerchip](/assets/LED_Interfacing/led_interfacing_1.png)

## Counter using LED
Verilog code snippet
```
reg [7:0] val;
always @(posedge clk)
begin
    if(reset) 
        val <= 0;
    else 
        val <= val + 1;
end
```
![@ cycle 71](/assets/LED_Interfacing/led_binary_counter.png)

## Lab
 To design a circuit that turns on each led for one clock cycle in row

Core Logic
```

```
![LED Lab](/assets/LED_Interfacing/led_lab.gif)

# Report By
- R.V.Rohinth Ram

# Acknowledgements
- Bala Dhinesh, Workshop Instructor
- Kunal Ghosh, Co-founder, VLSI System Design (VSD) Corp. Pvt. Ltd. - kunalpghosh@gmail.com


---
Thanks to Bala Dhinesh, Workshop Instructor

Reference: 
- https://github.com/BalaDhinesh/Digital-Design-on-FPGA--VSDOpen21
- https://github.com/BalaDhinesh/Virtual-FPGA-Lab
---