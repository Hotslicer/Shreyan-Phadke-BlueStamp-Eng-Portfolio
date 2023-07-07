# CNC Pen Plotter
Have you ever had a tedious school art project that your struggling to complete at 1:30 am? Do you have terrible handwriting that only you can read? Well, fear not! Introducing the CNC Pen Plotter: A machine that uses a mechanism of runners and belts to control a pen on the XY plane using CNC software! To learn more about how the CNC pen plotter was designed and built as well as see it in action read more below! 

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Shreyan P | Cupertino High School | Mechanical Engineering | Incoming Senior


<img width="478" alt="Screen Shot 2023-07-06 at 9 00 28 AM" src="https://github.com/Hotslicer/Shreyan-Phadke-BlueStamp-Eng-Portfolio/assets/63621669/5dc1659c-4a09-4142-8be4-d7472cca6b77">

# Modification

For my modification, my goal was to make my pen plotter able to switch colors. The concept was to be able to incorporate multiple colors into each image by switching the pen the machine was holding. The task was very daunting, requiring significant mechanical and firmware creation to support. 

*Still being Developed*
  
# Final Milestone. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/htjaKcD7Y0U" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For my final Milestone, my goal was for the pen plotter to be able to draw my own images and text. I considered this milestone complete when my plotter drew a complex image of Optimus Prime as well as the first paragraph of the US Declaration of Independence. The biggest challenge the final milestone presented was successfully converting images and text into .gcode files. This is a multi-step process:

1: Find an image, preferably line art/cartoon style

2: Rasterize the image using https://picsvg.com/

3: Upload the image to Inkscape v0.92.5

4: Upload the DoesBotAX4 extension to Inkscape

5: Convert the vector into a .gcode file for a servo-driven CNC

6: Upload the .gcode file into UGS (Universal GCode Sender) and connect the machine

7: Run UGS (Universal GCode Sender)

To create this final workflow the main challenge I faced was converting the vector .svg images into CNC coordinates. Almost all CNC compilers used commands for a separate motor to control the Z axis. However, my pen plotter only used a servo for Z-Axis. I tried several software, none of which worked. Finally, I found an extension for a vector drawing software called Inkscape, however, the extension was only supported by an older version of the software, version 0.92.5. This version was only compatible with Windows, and I had a Macbook. To solve this problem, I worked with my lead mentor to repair the BlueStamp Windows laptop by ordering new RAM and a battery. Finally, I was able to download Inkscape 0.92.5 and install the Ax4 Drawbot Extension for GCode conversion. 

Another challenge I faced was making my pen plotter draw actual text. Converting text into .gcode works differently than converting images. There is a specific text library called "Hershey Fonts" which are purpose-built vector CNC fonts. I found a separate Inkscape extension called "Eggbot" which included Hershey fonts, however, it was incompatible with my outdated version of Inkscape. To counteract this issue I decompiled the extension and modified it to run on older Inkscape versions. With this extension in place, my pen plotter could finally draw text. 


I learned a lot through this process. First I learned about the mechanical aspects of CNC milling, and how a pen can move anywhere in the XY plane just using 2 motors. I also learned about the firmware behind CNC. I learned about .svg files and rasterization, which represent an image as a series of mathematical functions that a computer can understand. I also learned about .gcode files which turn the math functions from .svg files into a series of thousands of coordinate points that the Arduino executes. Finally, I learned about how .gcode files are sent to the machine: through the UGS software. 


# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/0uyFmvGK-64" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For my second milestone, I finished the electrical wiring, set up the GRBL 0.9 software, and made my pen plotter draw sample images from existing GCode files. The pen plotter uses CNC mill software to turn images into coordinate points. It then moves the tip of the pen through the various coordinate points. A servo at the end lifts the pen when needed to create breaks in the image. With milestone #2 the function of the pen plotter has been fulfilled however the next step is to take my own images and convert them into GCode files for the Pen Plotter to draw. 

The most significant challenge I overcame with milestone 2 was the software setup. The X-Code Arduino uploader which added the GRBL software to the Arduino UnoMega needed Windows 10, however, I used Mac. I tried to use Bootcamp utility to get Windows on my Mac but after trying 3 different solutions bootcamp ultimately didn't work. I get around this I used my mentor's computer to upload the GRBL hex file to the Arduino. 

Another challenge was configuring the Universal GCode Platform (UGS) software to work with the Pen Plotter. I had to manually set up the software to recognize the Arduino and spent 4 hours troubleshooting to make the Aruino motors function from the software inputs. Through the series of firmware debugging, I learned more about the software behind CNC machining and about Arduino. 

Ultimately the machine works well and I am surprised by its precision despite having only the X, Y axis on a UP/DOWN/LEFT/RIGHT control scheme. It is able to draw very precise curved lines and text with great accuracy. 

**Milestone 2 Machine Operation**

<iframe width="560" height="315" src="https://www.youtube.com/embed/7r2xqhQgGDs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/AOHoNZSm_1M" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

My first milestone required me to complete the hardware portion of the project. This included the frame, runners, belt system, and motors. These parts make the pen tip able to freely move in the XY plane. To move in the X-Axis a singular motor runs while to move in the Y-Axis motors move in opposing directions. 

One of the most significant obstacles with this assembly was the litany of small M3 and M2 screws as well as the precise direction and orientation needed for each part for the whole system to function. M2 screws proved especially tedious to install due to their minuscule size however I learned to use a micro-tweazer and a magnetic screwdriver to easily install them. 

The remaining portion of the project involves finishing the electrical wiring and firmware so that the pen plotter is functional. By Jun 15th I will finish the wiring and have the plotter drawing a simple image. By Jun 17th I will be able to create my own images for me. 


# Schematics 

Arduino Uno Mega 2236

<img width="911" alt="arduinomap" src="https://github.com/Hotslicer/Shreyan-Phadke-BlueStamp-Eng-Portfolio/assets/63621669/afeb7b90-01e2-4f62-9bae-d885c1a539a5">


# Code

For code, I used the GRBL 0.9 software for CNC machines. This software is made to take .gcode files and make the CNC machine execute their command lists. 
Main firmware: https://codeshare.io/ZJPjEN

# Bill of Materials

Separated into different parts of the assembly

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Doesbot A4 Working Area XY Plotter Kit | Main Kit That Design Is Based On | $139.99 | <a href="https://www.amazon.com/Linear-Motion-15-748-Hardened-Printer/"> Link </a> |
|:--:|:--:|:--:|:--:|
| 8mm polished rods 450mm (2) | Used to guide the pen-block on the X-Axis | $13.99 | <a href="https://www.amazon.com/Linear-Motion-15-748-Hardened-Printer/"> Link </a> |
|:--:|:--:|:--:|:--:|
| 8mm polished rods 360mm | WUsed to guide the pen-block on the Y-Axis | $13.99 | <a href="https://www.amazon.com/dp/B0BB1KMTFH/ref=twister_B0B9Y4DWYT?_encoding=UTF8&psc=1/"> Link </a> |
|:--:|:--:|:--:|:--:|
| Black Acrylic Board (25) | Main construction material of pen block and motor mounts | $35.21 | <a href="https://www.amazon.com/Acrylic-Material-Plexiglass-Accessory-Thickness/dp/B07SZ23JSS?th=1/"> Link </a> |
|:--:|:--:|:--:|:--:|
| F624 flange bearings (10) | Used to hold the belt on the pen mounts | $10.39 | <a href="https://www.amazon.com/F624-ZZ-F624-2Z-Flanged-Bearings-4x13x5mm/dp/B09NTQMKSJ/"> Link </a> |
|:--:|:--:|:--:|:--:|
| Synchronous Wheels (2) | Attachement to the motor that allows for belt attachement | $11.89 | <a href="https://www.amazon.com/Zeberoxyz-Synchronous-Aluminum-Timing-Pulley/dp/B09YYQH1CZ?th=1/"> Link </a> |
|:--:|:--:|:--:|:--:|
| 1.5M Timing Belt | Connects the motors and the Y axis runner platform.| $12.76 | <a href="https://www.gprindustrial.com/en/5mm-pitch-5m-timing-belts/"> Link </a> |
|:--:|:--:|:--:|:--:|
| Black Cable Ties (25) | Used to hold various wires and connectors in place | $6.11 | <a href="https://www.amazon.com/Black-Double-Cable-Management-Nylon/dp/B07BH6X4N8/"> Link </a> |
|:--:|:--:|:--:|:--:|
| Linear Guide Assembly | Holds the pen holder so that it can move up/down from servo | $9.86 | <a href="https://www.amazon.com/LWL7B-Miniature-Linear-Guide-Width/dp/B07GCDQ2TG/"> Link </a> |
|:--:|:--:|:--:|:--:|
| 3D Pen Holder| 3d printed holder for the pen | N/A | N/A |
|:--:|:--:|:--:|:--:|


# Starter Project

<iframe width="560" height="315" src="https://www.youtube.com/embed/qXI4fr07pTI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

My starter project was a Simon Says game from a building kit. 

BOM

| **Part** | **Description** |
|:--:|:--:|
| Simon PCB Board | "Motherboard" of system. All components connect here |
| Buzzer | Used to emit noises and sound effects | 
| 0.1uF capacitator | Used to store electrical energy | 
| 10k Resistor (2) | Reduces voltage by 10,000 ohms | 
| LEDs (4) | Emits light used in system operation | 
| Slide Switch (2) | Used to turn the machine on/off and toggle sound | 
| Battery Clips (2) | Cathode + Anode used to deliver battery charge | 
| AA Batteries (2) | Power Source of the System | 
| ATMega Microcontroller | Conducts system operations | 
| Standoffers & Screws (4) | Used to mount the button board to the PCB | 

The assembly was completed in 45 minutes and taught me important concepts in soldering various holes and wire connections as well as different hardware such as Speakers, microcontrollers, and batteries. One challenge I faced was correctly aligning the LEDs with their polarity. 


Thank you for reading



