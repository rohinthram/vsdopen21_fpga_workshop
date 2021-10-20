# VSDOpen21 FPGA Workshop
![VSDOpen21](/assets/vsdopentutorial_banner.png)

---
![Lab Try](/assets/lab_try.png)
![Final Lab](/assets/traffic_light_control.gif)

---

This is the report for the one day workshop on "Digital Design using `virtual` FPGA"


# Contents
 - [Platform Used](#Platform-Used)
 - [Introduction](#Introduction)
 - [Theory](#Theory)
 - [Problem Statement](#Problem-Statement)
 - [Interfacing LED](#Interfacing-LED)
 - [Interfacing Seven Segment Display](#Interfacing-Seven-Segment-Display)
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
m4_define(M4_BOARD, <board_no>)
```
replace `<board_no>` with number provided above to use respective board

After this the board is initialized using 
```
m4+fpga_init()
```

- Interfacing LED
Makerchip instance to inteface LED
```
m4+fpga_led(*led)
```
where *led will be the register to store state of LED(on -1 , off - 0)

- Interfacing Seven Segment Display
Makerchip instance to inteface LED
```
m4+fpga_sseg(*digit, *segment, *dp)
```
- digit is to *enable a seven segment display(digit)* (register length depends on digit availabel)
- segment is to *enable the segment of the digit enabled* (7 bit register)
- dp is to *enable the decimal point* 


# Theory
- Using makerchip
- Verilog basis
    - use of `wire` and `reg`
    - *blocking* and *non-blocking* assignment
    - case statement
- Basics of Seven Segment Display
    - Common cathode
- Combinational Logic - use of `wire`
- Sequential Logic - use of `reg`
    - Mealy and moore - Finite State Machine
- Synchronous and Asynchronous reset

# Problem Statement
 To design a traffic light control system on a 4 way road

 8 Seconds(or clock pulses) Green light
 4 Seconds  Yellow light

Direction of signal change
Nort => South => East => West
---

# Interfacing LED
Verilog code
```
reg [7:0] led = 8'b10010101;
```
Interface LED with
```
m4+fpga_led(*led)
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
reg [7:0] val;
always @(posedge clk, posedge reset)
begin
    if(reset || val == 8'b10000000) 
        val <= 1;
    else 
        val <= val << 1;
end
```
![LED Lab](/assets/LED_Interfacing/led_lab.gif)


# Interfacing Seven Segment Display
Interface 7 Seg Display with 
```
m4+fpga_sseg(*digit, *segment, *dp)
```
(Assuming 7 seg displays are active low enabled)

## Lab
To design a hexadecimal counter
Constants
```
reg [3:0] digit;
reg [6:0] seg;
reg dp;         //all active low

reg [4:0] cnt = 0;
```
```
parameter [6:0] zero = 7'b0000001,          one=7'b1001111, two=7'b0010010,
                   three=7'b0000110, four=7'b1001100, five=7'b0100100,
                   six=7'b0100000, seven=7'b0001111, eight=7'b0000000,
                   nine=7'b0000100, a=7'b0000010, b=7'b1100000, c=7'b0110001,
                   d = 7'b1000010, e=7'b0010000, f=7'b0111000;
```
Core logic
```
always @(posedge clk, posedge reset)
      begin
         if(reset)
         begin
         	digit <= 0;
            seg <= 0;
            dp <= 0;
            cnt <= 0;
         end
         else
         begin
            dp <= 1;
            case(cnt)
               0: seg <= zero;
               1: seg <= one;
               2: seg <= two;
               3: seg <= three;
               4: seg <= four;
               5: seg <= five;
               6: seg <= six;
               7: seg <= seven;
               8: seg <= eight;
               9: seg <= nine;
               10: seg <= a;
               11: seg <= b;
               12: seg <= c;
               13: seg <= d;
               14: seg <= e;
               15: seg <= f;
               default: seg <= 0;
            endcase
            cnt <= cnt + 1;
            if(cnt>=15) cnt <= 0;
			end
         
      end
```

![Hex Counter](/assets/seven_seg_lab.gif)

# Problem Solution
Assume 
- North, South, East, West = Left to right 7 Segment Display 
- bottom segemnt - Green light
- Middle segment - Yellow light
- No segment - Red light

Variables
```
wire [15:0] led;
reg [3:0] digit;
reg [6:0] segment;
wire  dp = 1;

reg [2:0] state;
reg [2:0] count;
```
State
```
   parameter [2:0] NORTH	=	3'b000;
   parameter [2:0] NORTH_Y	=	3'b001;
   parameter [2:0] SOUTH	=	3'b010;
   parameter [2:0] SOUTH_Y	=	3'b011;
   parameter [2:0] EAST		=	3'b100;
   parameter [2:0] EAST_Y	=	3'b101;
   parameter [2:0] WEST		=	3'b110;
   parameter [2:0] WEST_Y	=	3'b111;
```

Core logic
```
always @(posedge clk, posedge reset)
     begin
        if (reset)
            begin
               state <= NORTH;
               count <= 0;
               
            end
        else
            begin
                case (state)
                NORTH :
                    begin
                       digit <= 4'b0111;
                       segment <= 7'b1110111;
                       
                        if(count>=7)
                        begin
                           count <= 0;
                           state <= NORTH_Y;
                        end
                       else
                          count <= count + 1;
                    end

                NORTH_Y :
                    begin
                        digit <= 4'b0111;
                        segment <= 7'b1111110;
                        if(count>=3)
                        begin
                           count <= 0;
                           state <= SOUTH;
                        end
                        else
                           count <= count + 1;
                    end
                   
               SOUTH :
                    begin
                       digit <= 4'b1011;
                       segment <= 7'b1110111;
                       
                        if(count>=7)
                        begin
                           count <= 0;
                           state <= SOUTH_Y;
                        end
                       else
                           count <= count + 1;
                    end

                SOUTH_Y :
                    begin
                        digit <= 4'b1011;
                        segment <= 7'b1111110;
                        if(count>=3)
                        begin
                           count <= 0;
                           state <= EAST;
                        end
                       else
                           count <= count + 1;
                       
                    end
                   
                EAST :
                    begin
                       digit <= 4'b1101;
                       segment <= 7'b1110111;
                       
                        if(count>=7)
                        begin
                           count <= 0;
                           state <= EAST_Y;
                        end
                       else
                           count <= count + 1;
                    end
                EAST_Y :
                    begin
                        digit <= 4'b1101;
                        segment <= 7'b1111110;
                        if(count>=3)
                        begin
                           count <= 0;
                           state <= WEST;
                        end
                       else
                           count <= count + 1;
                       
                    end
                WEST :
                    begin
                       digit <= 4'b1110;
                       segment <= 7'b1110111;
                       
                        if(count>=7)
                        begin
                           count <= 0;
                           state <= WEST_Y;
                        end
                       else
                           count <= count + 1;
                    end
                WEST_Y :
                    begin
                        digit <= 4'b1110;
                        segment <= 7'b1111110;
                        if(count>=3)
                        begin
                           count <= 0;
                           state <= NORTH;
                        end
                       else
                           count <= count + 1;
                       
                    end
            endcase
				
        end 
    end 
	assign led = count;
```
![Traffic Light Controller](/assets/traffic_light_control.gif)

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