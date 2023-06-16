# CNC Pen Plotter
Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails!

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Shreyan P | Cupertino High School | Mechanical Engineering | Incoming Senior


**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone
For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


# Second Milestone

For my second milestone I finished the electrical wiring, setup the GRBL 0.9 software and made my pen plotter draw sample images from existing GCode files. The pen plotter uses CNC mill software to turn images into coordiante points. It then moves the tip of the pen through the various coordinate points. A servo at the end lifts the pen when needed to create breaks in the image. Overall the function of the pen plotter has been fulfilled however the next step is to take my own images and convert them into GCode files for the Pen Plotter to draw. 

The most significant challenge I overcame with milestone 2 was the software setup. The X-Code arduino uploader which added the GRBL software to the Arduino UnoMega needed Windows 10, however I used Mac. I tried to use bootcamp utility to get windows on my Mac but after trying 3 different solutions bootcamp ultimately didn't work. I get around this I used my mentors computer to upload the GRBL hex file to the Arduino. 

Another challenge was configuring the Universal GCode Platform (UGS) software to work with the Pen Plotter. I had to manually setup the software to recognize the Arduino and spent 4 hours troubleshooting to make the Aruino motors function from the software inputs. Through the series of firmware debugging I learned more about the software behind CNC machining and about Arduino. 

Ultimately the machine works well and I am surpised by it's precision despite having only the X, Y axis on a UP/DOWN/LEFT/RIGHT control scheme. It is able to draw very precise curved lines and text with great accuracy. 

**Milestone 2 Machine Operation**

<iframe width="560" height="315" src="https://www.youtube.com/embed/7r2xqhQgGDs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>



**Milestone 2 Video**


<iframe width="560" height="315" src="https://www.youtube.com/embed/0uyFmvGK-64" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

# First Milestone
My first milestone required me to complete the hardware portion of the project. This included the frame,runners, belt system and motors. These parts make the pen-tip be able to freely move in the XY plane. To move in the X-Axis a singular motor runs while to move in the Y-Axis motors move in opposing directions. 

One of the most significant obstalces with this assembly was the litany of small M3 and M2 screws as well as the precise direction and orientation needed of each part for the whole system to funciton. M2 screws proved especially tedious to install due to their miniscule size however I learned to use micro-tweazer and a magnetic screwdriver to easily install them. 

The remaining portion of the project involves finishing the electrical wiring and firmware so that the pen plotter is functional. By Jun 15th I will finish the wiring and have the plotter drawing a simple image. By Jun 17th it will be able to create my own images for me. 

**Milestone Video**

<iframe width="560" height="315" src="https://www.youtube.com/embed/AOHoNZSm_1M" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code

For code we used the GRBL 0.9 software for CNC machines. This software is made to take .gcode files and make the CNC machine execute their command lists. 
Main firmware: https://codeshare.io/ZJPjEN

# Bill of Materials

Seperated into different parts of the assembly


| **Part** | **Note** | **Price** | **Link** |
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

My starter project was a Simon Says game from a building kit. 

BOM

| **Part** | **Usage** |
|:--:|:--:|:--:|:--:|
| Simon PCB Board | "Motherboard" of system. All components connect here |
|:--:|:--:|:--:|:--:|
| Buzzer | Used to emit noises and sound effects | 
|:--:|:--:|:--:|:--:|
| 0.1uF capacitator | Used to store electrical energy | 
|:--:|:--:|:--:|:--:|
| 10k Resistor (2) | Reduces voltage by 10,000 ohms | 
|:--:|:--:|:--:|:--:|
| LEDs (4) | Emits light used in system operation | 
|:--:|:--:|:--:|:--:|
| Slide Switch (2) | Used to turn the machine on/off and toggle sound | 
|:--:|:--:|:--:|:--:|
| Battery Clips (2) | Cathode + Anode used to deliver battery charge | 
|:--:|:--:|:--:|:--:|
| AA Batteries (2) | Power Source of the System | 
|:--:|:--:|:--:|:--:|
| ATMega Microcontroller | Conducts system operations | 
|:--:|:--:|:--:|:--:|
| Standoffers & Screws (4) | Used to mount the button board to the PCB | 
|:--:|:--:|:--:|:--:|

The assembly was complete in 45 minute and taught me important concepts in soldering various holes and wire connections as well as different hardware such as Speakers, microcontrollers and batteries. One challenge I faced was correctly aligning the LEDs with their polarity. 
For your first milestone, describe what your project is and how you plan to build it. You can include:

Video:

<iframe width="560" height="315" src="https://www.youtube.com/embed/BwH0mT4WVgo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
