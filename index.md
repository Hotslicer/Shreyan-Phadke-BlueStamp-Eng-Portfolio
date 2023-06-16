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
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c++

#define AXIS_COMMAND_NONE 0
#define AXIS_COMMAND_NON_MODAL 1 
#define AXIS_COMMAND_MOTION_MODE 2
#define AXIS_COMMAND_TOOL_LENGTH_OFFSET 3 // *Undefined but required

// Declare gc extern struct
parser_state_t gc_state;
parser_block_t gc_block;

#define FAIL(status) return(status);


void gc_init() 
{
  memset(&gc_state, 0, sizeof(gc_state));
  
  // Load default G54 coordinate system.
  if (!(settings_read_coord_data(gc_state.modal.coord_select,gc_state.coord_system))) { 
    report_status_message(STATUS_SETTING_READ_FAIL); 
  } 
}


// Sets g-code parser position in mm. Input in steps. Called by the system abort and hard
// limit pull-off routines.
void gc_sync_position() 
{
  system_convert_array_steps_to_mpos(gc_state.position,sys.position);
}


static uint8_t gc_check_same_position(float *pos_a, float *pos_b) 
{
  uint8_t idx;
  for (idx=0; idx<N_AXIS; idx++) {
    if (pos_a[idx] != pos_b[idx]) { return(false); }
  }
  return(true);
}
         

uint8_t gc_execute_line(char *line) 
{

     
  memset(&gc_block, 0, sizeof(gc_block)); // Initialize the parser block struct.
  memcpy(&gc_block.modal,&gc_state.modal,sizeof(gc_modal_t)); // Copy current modes
  uint8_t axis_command = AXIS_COMMAND_NONE;
  uint8_t axis_0, axis_1, axis_linear;
  uint8_t coord_select = 0; // Tracks G10 P coordinate selection for execution
  float coordinate_data[N_AXIS]; // Multi-use variable to store coordinate data for execution
  float parameter_data[N_AXIS]; // Multi-use variable to store parameter data for execution
 
     
  uint8_t word_bit; // Bit-value for assigning tracking variables
  uint8_t char_counter = 0;  
  char letter;
  float value;
  uint8_t int_value = 0;
  uint16_t mantissa = 0;

  while (line[char_counter] != 0) { // Loop until no more g-code words in line.
          
      case 'G':
        // Determine 'G' command and its modal group
        switch(int_value) {
          case 10: case 28: case 30: case 92: 
            // Check for G10/28/30/92 being called with G0/1/2/3/38 on same block.
            // * G43.1 is also an axis command but is not explicitly defined this way.
            if (mantissa == 0) { // Ignore G28.1, G30.1, and G92.1
              if (axis_command) { FAIL(STATUS_GCODE_AXIS_COMMAND_CONFLICT); } // [Axis word/command conflict]
              axis_command = AXIS_COMMAND_NON_MODAL;
            }
            // No break. Continues to next line.
          case 4: case 53: 
            word_bit = MODAL_GROUP_G0; 
            switch(int_value) {
              case 4: gc_block.non_modal_command = NON_MODAL_DWELL; break; // G4
              case 10: gc_block.non_modal_command = NON_MODAL_SET_COORDINATE_DATA; break; // G10
              case 28:
                switch(mantissa) {
                  case 0: gc_block.non_modal_command = NON_MODAL_GO_HOME_0; break;  // G28
                  case 10: gc_block.non_modal_command = NON_MODAL_SET_HOME_0; break; // G28.1
                  default: FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); // [Unsupported G28.x command]
                }
                mantissa = 0; // Set to zero to indicate valid non-integer G command.
                break;
              case 30: 
                switch(mantissa) {
                  case 0: gc_block.non_modal_command = NON_MODAL_GO_HOME_1; break;  // G30
                  case 10: gc_block.non_modal_command = NON_MODAL_SET_HOME_1; break; // G30.1
                  default: FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); // [Unsupported G30.x command]
                }
                mantissa = 0; // Set to zero to indicate valid non-integer G command.
                break;
              case 53: gc_block.non_modal_command = NON_MODAL_ABSOLUTE_OVERRIDE; break; // G53
              case 92: 
                switch(mantissa) {
                  case 0: gc_block.non_modal_command = NON_MODAL_SET_COORDINATE_OFFSET; break; // G92
                  case 10: gc_block.non_modal_command = NON_MODAL_RESET_COORDINATE_OFFSET; break; // G92.1
                  default: FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); // [Unsupported G92.x command]
                }
                mantissa = 0; // Set to zero to indicate valid non-integer G command.
                break;      
            }
            break;
          case 0: case 1: case 2: case 3: case 38: 
            // Check for G0/1/2/3/38 being called with G10/28/30/92 on same block.
            // * G43.1 is also an axis command but is not explicitly defined this way.
            if (axis_command) { FAIL(STATUS_GCODE_AXIS_COMMAND_CONFLICT); } // [Axis word/command conflict]
            axis_command = AXIS_COMMAND_MOTION_MODE; 
            // No break. Continues to next line.
          case 80: 
            word_bit = MODAL_GROUP_G1; 
            switch(int_value) {
              case 0: gc_block.modal.motion = MOTION_MODE_SEEK; break; // G0
              case 1: gc_block.modal.motion = MOTION_MODE_LINEAR; break; // G1
              case 2: gc_block.modal.motion = MOTION_MODE_CW_ARC; break; // G2
              case 3: gc_block.modal.motion = MOTION_MODE_CCW_ARC; break; // G3
              case 38: 
                switch(mantissa) {
                  case 20: gc_block.modal.motion = MOTION_MODE_PROBE_TOWARD; break; // G38.2
                  case 30: gc_block.modal.motion = MOTION_MODE_PROBE_TOWARD_NO_ERROR; break; // G38.3
                  case 40: gc_block.modal.motion = MOTION_MODE_PROBE_AWAY; break; // G38.4
                  case 50: gc_block.modal.motion = MOTION_MODE_PROBE_AWAY_NO_ERROR; break; // G38.5
                  default: FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); // [Unsupported G38.x command]
                }
                mantissa = 0; // Set to zero to indicate valid non-integer G command.
                break;
              case 80: gc_block.modal.motion = MOTION_MODE_NONE; break; // G80
            }            
            break;
          case 17: case 18: case 19: 
            word_bit = MODAL_GROUP_G2; 
            switch(int_value) {
              case 17: gc_block.modal.plane_select = PLANE_SELECT_XY; break;
              case 18: gc_block.modal.plane_select = PLANE_SELECT_ZX; break;
              case 19: gc_block.modal.plane_select = PLANE_SELECT_YZ; break;
            }
            break;
          case 90: case 91: 
            if (mantissa == 0) {
              word_bit = MODAL_GROUP_G3; 
              if (int_value == 90) { gc_block.modal.distance = DISTANCE_MODE_ABSOLUTE; } // G90
              else { gc_block.modal.distance = DISTANCE_MODE_INCREMENTAL; } // G91
            } else {
              word_bit = MODAL_GROUP_G4;
              if ((mantissa != 10) || (int_value == 90)) { FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); } // [G90.1 not supported]
              mantissa = 0; // Set to zero to indicate valid non-integer G command.
              // Otherwise, arc IJK incremental mode is default. G91.1 does nothing.
            }
            break;
          case 93: case 94: 
            word_bit = MODAL_GROUP_G5; 
            if (int_value == 93) { gc_block.modal.feed_rate = FEED_RATE_MODE_INVERSE_TIME; } // G93
            else { gc_block.modal.feed_rate = FEED_RATE_MODE_UNITS_PER_MIN; } // G94
            break;
          case 20: case 21: 
            word_bit = MODAL_GROUP_G6; 
            if (int_value == 20) { gc_block.modal.units = UNITS_MODE_INCHES; }  // G20
            else { gc_block.modal.units = UNITS_MODE_MM; } // G21
            break;
          case 40:
            word_bit = MODAL_GROUP_G7;
            // NOTE: Not required since cutter radius compensation is always disabled. Only here
            // to support G40 commands that often appear in g-code program headers to setup defaults.
            // gc_block.modal.cutter_comp = CUTTER_COMP_DISABLE; // G40
            break;
          case 43: case 49:
            word_bit = MODAL_GROUP_G8;
            // NOTE: The NIST g-code standard vaguely states that when a tool length offset is changed,
            // there cannot be any axis motion or coordinate offsets updated. Meaning G43, G43.1, and G49
            // all are explicit axis commands, regardless if they require axis words or not. 
            if (axis_command) { FAIL(STATUS_GCODE_AXIS_COMMAND_CONFLICT); } // [Axis word/command conflict] }
            axis_command = AXIS_COMMAND_TOOL_LENGTH_OFFSET;
            if (int_value == 49) { // G49
              gc_block.modal.tool_length = TOOL_LENGTH_OFFSET_CANCEL; 
            } else if (mantissa == 10) { // G43.1
              gc_block.modal.tool_length = TOOL_LENGTH_OFFSET_ENABLE_DYNAMIC;
            } else { FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); } // [Unsupported G43.x command]
            mantissa = 0; // Set to zero to indicate valid non-integer G command.
            break;
          case 54: case 55: case 56: case 57: case 58: case 59: 
            // NOTE: G59.x are not supported. (But their int_values would be 60, 61, and 62.)
            word_bit = MODAL_GROUP_G12;
            gc_block.modal.coord_select = int_value-54; // Shift to array indexing.
            break;
          case 61:
            word_bit = MODAL_GROUP_G13;
            if (mantissa != 0) { FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); } // [G61.1 not supported]
            // gc_block.modal.control = CONTROL_MODE_EXACT_PATH; // G61
            break;
          default: FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); // [Unsupported G command]
        }      
        if (mantissa > 0) { FAIL(STATUS_GCODE_COMMAND_VALUE_NOT_INTEGER); } // [Unsupported or invalid Gxx.x command]
        // Check for more than one command per modal group violations in the current block
        // NOTE: Variable 'word_bit' is always assigned, if the command is valid.
        if ( bit_istrue(command_words,bit(word_bit)) ) { FAIL(STATUS_GCODE_MODAL_GROUP_VIOLATION); }
        command_words |= bit(word_bit);
        break;
        
      case 'M':
      
        // Determine 'M' command and its modal group
        if (mantissa > 0) { FAIL(STATUS_GCODE_COMMAND_VALUE_NOT_INTEGER); } // [No Mxx.x commands]
        switch(int_value) {
          case 0: case 1: case 2: case 30: 
            word_bit = MODAL_GROUP_M4; 
            switch(int_value) {
              case 0: gc_block.modal.program_flow = PROGRAM_FLOW_PAUSED; break; // Program pause
              case 1: break; // Optional stop not supported. Ignore.
              case 2: case 30: gc_block.modal.program_flow = PROGRAM_FLOW_COMPLETED; break; // Program end and reset 
            }
            break;
          #ifndef USE_SPINDLE_DIR_AS_ENABLE_PIN
            case 4: 
          #endif
          case 3: case 5:
            word_bit = MODAL_GROUP_M7; 
            switch(int_value) {
              case 3: gc_block.modal.spindle = SPINDLE_ENABLE_CW; break;
              #ifndef USE_SPINDLE_DIR_AS_ENABLE_PIN
                case 4: gc_block.modal.spindle = SPINDLE_ENABLE_CCW; break;
              #endif
              case 5: gc_block.modal.spindle = SPINDLE_DISABLE; break;
            }
            break;            
         #ifdef ENABLE_M7  
          case 7:
         #endif
          case 8: case 9:
            word_bit = MODAL_GROUP_M8; 
            switch(int_value) {      
             #ifdef ENABLE_M7
              case 7: gc_block.modal.coolant = COOLANT_MIST_ENABLE; break;
             #endif
              case 8: gc_block.modal.coolant = COOLANT_FLOOD_ENABLE; break;
              case 9: gc_block.modal.coolant = COOLANT_DISABLE; break;
            }
            break;
          default: FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); // [Unsupported M command]
        }
      
        // Check for more than one command per modal group violations in the current block
        // NOTE: Variable 'word_bit' is always assigned, if the command is valid.
        if ( bit_istrue(command_words,bit(word_bit)) ) { FAIL(STATUS_GCODE_MODAL_GROUP_VIOLATION); }
        command_words |= bit(word_bit);
        break;
      
      // NOTE: All remaining letters assign values.
      default: 
  
        /* Non-Command Words: This initial parsing phase only checks for repeats of the remaining
           legal g-code words and stores their value. Error-checking is performed later since some
           words (I,J,K,L,P,R) have multiple connotations and/or depend on the issued commands. */
        switch(letter){
          // case 'A': // Not supported
          // case 'B': // Not supported
          // case 'C': // Not supported
          // case 'D': // Not supported
          case 'F': word_bit = WORD_F; gc_block.values.f = value; break;
          // case 'H': // Not supported
          case 'I': word_bit = WORD_I; gc_block.values.ijk[X_AXIS] = value; ijk_words |= (1<<X_AXIS); break;
          case 'J': word_bit = WORD_J; gc_block.values.ijk[Y_AXIS] = value; ijk_words |= (1<<Y_AXIS); break;
          case 'K': word_bit = WORD_K; gc_block.values.ijk[Z_AXIS] = value; ijk_words |= (1<<Z_AXIS); break;
          case 'L': word_bit = WORD_L; gc_block.values.l = int_value; break;
          case 'N': word_bit = WORD_N; gc_block.values.n = trunc(value); break;
          case 'P': word_bit = WORD_P; gc_block.values.p = value; break;
          // NOTE: For certain commands, P value must be an integer, but none of these commands are supported.
          // case 'Q': // Not supported
          case 'R': word_bit = WORD_R; gc_block.values.r = value; break;
          case 'S': word_bit = WORD_S; gc_block.values.s = value; break;
          case 'T': word_bit = WORD_T; break; // gc.values.t = int_value;
          case 'X': word_bit = WORD_X; gc_block.values.xyz[X_AXIS] = value; axis_words |= (1<<X_AXIS); break;
          case 'Y': word_bit = WORD_Y; gc_block.values.xyz[Y_AXIS] = value; axis_words |= (1<<Y_AXIS); break;
          case 'Z': word_bit = WORD_Z; gc_block.values.xyz[Z_AXIS] = value; axis_words |= (1<<Z_AXIS); break;
          default: FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND);
        } 
        
        // NOTE: Variable 'word_bit' is always assigned, if the non-command letter is valid.
        if (bit_istrue(value_words,bit(word_bit))) { FAIL(STATUS_GCODE_WORD_REPEATED); } // [Word repeated]
        // Check for invalid negative values for words F, N, P, T, and S.
        // NOTE: Negative value check is done here simply for code-efficiency.
        if ( bit(word_bit) & (bit(WORD_F)|bit(WORD_N)|bit(WORD_P)|bit(WORD_T)|bit(WORD_S)) ) {
          if (value < 0.0) { FAIL(STATUS_NEGATIVE_VALUE); } // [Word value cannot be negative]
        }
        value_words |= bit(word_bit); // Flag to indicate parameter assigned.
      
    }   
  } 
  // Parsing complete!
  

  */  
  
  // [0. Non-specific/common error-checks and miscellaneous setup]: 
  
  // Determine implicit axis command conditions. Axis words have been passed, but no explicit axis
  // command has been sent. If so, set axis command to current motion mode.
  if (axis_words) {
    if (!axis_command) { axis_command = AXIS_COMMAND_MOTION_MODE; } // Assign implicit motion-mode
  }
  
  // Check for valid line number N value.
  if (bit_istrue(value_words,bit(WORD_N))) {
    // Line number value cannot be less than zero (done) or greater than max line number.
    if (gc_block.values.n > MAX_LINE_NUMBER) { FAIL(STATUS_GCODE_INVALID_LINE_NUMBER); } // [Exceeds max line number]
  }
  // bit_false(value_words,bit(WORD_N)); // NOTE: Single-meaning value word. Set at end of error-checking.
  
  // Track for unused words at the end of error-checking.
  // NOTE: Single-meaning value words are removed all at once at the end of error-checking, because
  // they are always used when present. This was done to save a few bytes of flash. For clarity, the
  // single-meaning value words may be removed as they are used. Also, axis words are treated in the
  // same way. If there is an explicit/implicit axis command, XYZ words are always used and are 
  // are removed at the end of error-checking.  
  
  // [1. Comments ]: MSG's NOT SUPPORTED. Comment handling performed by protocol.
  
  // [2. Set feed rate mode ]: G93 F word missing with G1,G2/3 active, implicitly or explicitly. Feed rate
  //   is not defined after switching to G94 from G93.
  if (gc_block.modal.feed_rate == FEED_RATE_MODE_INVERSE_TIME) { // = G93
    // NOTE: G38 can also operate in inverse time, but is undefined as an error. Missing F word check added here.
    if (axis_command == AXIS_COMMAND_MOTION_MODE) { 
      if ((gc_block.modal.motion != MOTION_MODE_NONE) || (gc_block.modal.motion != MOTION_MODE_SEEK)) {
        if (bit_isfalse(value_words,bit(WORD_F))) { FAIL(STATUS_GCODE_UNDEFINED_FEED_RATE); } // [F word missing]
      }
    }
    // NOTE: It seems redundant to check for an F word to be passed after switching from G94 to G93. We would
    // accomplish the exact same thing if the feed rate value is always reset to zero and undefined after each
    // inverse time block, since the commands that use this value already perform undefined checks. This would
    // also allow other commands, following this switch, to execute and not error out needlessly. This code is 
    // combined with the above feed rate mode and the below set feed rate error-checking.

    // [3. Set feed rate ]: F is negative (done.)
    // - In inverse time mode: Always implicitly zero the feed rate value before and after block completion.
    // NOTE: If in G93 mode or switched into it from G94, just keep F value as initialized zero or passed F word 
    // value in the block. If no F word is passed with a motion command that requires a feed rate, this will error 
    // out in the motion modes error-checking. However, if no F word is passed with NO motion command that requires
    // a feed rate, we simply move on and the state feed rate value gets updated to zero and remains undefined.
  } else { // = G94
    // - In units per mm mode: If F word passed, ensure value is in mm/min, otherwise push last state value.
    if (gc_state.modal.feed_rate == FEED_RATE_MODE_UNITS_PER_MIN) { // Last state is also G94
      if (bit_istrue(value_words,bit(WORD_F))) {
        if (gc_block.modal.units == UNITS_MODE_INCHES) { gc_block.values.f *= MM_PER_INCH; }
      } else {
        gc_block.values.f = gc_state.feed_rate; // Push last state feed rate
      }
    } // Else, switching to G94 from G93, so don't push last state feed rate. Its undefined or the passed F word value.
  } 
  // bit_false(value_words,bit(WORD_F)); // NOTE: Single-meaning value word. Set at end of error-checking.
  
  // [4. Set spindle speed ]: S is negative (done.)
  if (bit_isfalse(value_words,bit(WORD_S))) { gc_block.values.s = gc_state.spindle_speed; }
  // bit_false(value_words,bit(WORD_S)); // NOTE: Single-meaning value word. Set at end of error-checking.
    
  // [5. Select tool ]: NOT SUPPORTED. Only tracks value. T is negative (done.) Not an integer. Greater than max tool value.
  // bit_false(value_words,bit(WORD_T)); // NOTE: Single-meaning value word. Set at end of error-checking.

  // [6. Change tool ]: N/A
  // [7. Spindle control ]: N/A
  // [8. Coolant control ]: N/A
  // [9. Enable/disable feed rate or spindle overrides ]: NOT SUPPORTED.
  
  // [10. Dwell ]: P value missing. P is negative (done.) NOTE: See below.
  if (gc_block.non_modal_command == NON_MODAL_DWELL) {
    if (bit_isfalse(value_words,bit(WORD_P))) { FAIL(STATUS_GCODE_VALUE_WORD_MISSING); } // [P word missing]
    bit_false(value_words,bit(WORD_P));
  }
  
  // [11. Set active plane ]: N/A
  switch (gc_block.modal.plane_select) {
    case PLANE_SELECT_XY:
      axis_0 = X_AXIS;
      axis_1 = Y_AXIS;
      axis_linear = Z_AXIS;
      break;
    case PLANE_SELECT_ZX:
      axis_0 = Z_AXIS;
      axis_1 = X_AXIS;
      axis_linear = Y_AXIS;
      break;
    default: // case PLANE_SELECT_YZ:
      axis_0 = Y_AXIS;
      axis_1 = Z_AXIS;
      axis_linear = X_AXIS;
  }   
            
  // [12. Set length units ]: N/A
  // Pre-convert XYZ coordinate values to millimeters, if applicable.
  uint8_t idx;
  if (gc_block.modal.units == UNITS_MODE_INCHES) {
    for (idx=0; idx<N_AXIS; idx++) { // Axes indices are consistent, so loop may be used.
      if (bit_istrue(axis_words,bit(idx)) ) {
        gc_block.values.xyz[idx] *= MM_PER_INCH;
      }
    }
  }
  
  // [13. Cutter radius compensation ]: G41/42 NOT SUPPORTED. Error, if enabled while G53 is active.
  // [G40 Errors]: G2/3 arc is programmed after a G40. The linear move after disabling is less than tool diameter.
  //   NOTE: Since cutter radius compensation is never enabled, these G40 errors don't apply. Grbl supports G40 
  //   only for the purpose to not error when G40 is sent with a g-code program header to setup the default modes.
  
  // [14. Cutter length compensation ]: G43 NOT SUPPORTED, but G43.1 and G49 are. 
  // [G43.1 Errors]: Motion command in same line. 
  //   NOTE: Although not explicitly stated so, G43.1 should be applied to only one valid 
  //   axis that is configured (in config.h). There should be an error if the configured axis
  //   is absent or if any of the other axis words are present.
  if (axis_command == AXIS_COMMAND_TOOL_LENGTH_OFFSET ) { // Indicates called in block.
    if (gc_block.modal.tool_length == TOOL_LENGTH_OFFSET_ENABLE_DYNAMIC) {
      if (axis_words ^ (1<<TOOL_LENGTH_OFFSET_AXIS)) { FAIL(STATUS_GCODE_G43_DYNAMIC_AXIS_ERROR); }
    }
  }
  
  // [15. Coordinate system selection ]: *N/A. Error, if cutter radius comp is active.
  // TODO: An EEPROM read of the coordinate data may require a buffer sync when the cycle
  // is active. The read pauses the processor temporarily and may cause a rare crash. For 
  // future versions on processors with enough memory, all coordinate data should be stored
  // in memory and written to EEPROM only when there is not a cycle active.
  memcpy(coordinate_data,gc_state.coord_system,sizeof(gc_state.coord_system));
  if ( bit_istrue(command_words,bit(MODAL_GROUP_G12)) ) { // Check if called in block
    if (gc_block.modal.coord_select > N_COORDINATE_SYSTEM) { FAIL(STATUS_GCODE_UNSUPPORTED_COORD_SYS); } // [Greater than N sys]
    if (gc_state.modal.coord_select != gc_block.modal.coord_select) {
      if (!(settings_read_coord_data(gc_block.modal.coord_select,coordinate_data))) { FAIL(STATUS_SETTING_READ_FAIL); } 
    }
  }
  
  // [16. Set path control mode ]: N/A. Only G61. G61.1 and G64 NOT SUPPORTED.
  // [17. Set distance mode ]: N/A. Only G91.1. G90.1 NOT SUPPORTED.
  // [18. Set retract mode ]: NOT SUPPORTED.
  
  // [19. Remaining non-modal actions ]: Check go to predefined position, set G10, or set axis offsets.
  // NOTE: We need to separate the non-modal commands that are axis word-using (G10/G28/G30/G92), as these
  // commands all treat axis words differently. G10 as absolute offsets or computes current position as
  // the axis value, G92 similarly to G10 L20, and G28/30 as an intermediate target position that observes
  // all the current coordinate system and G92 offsets. 
  switch (gc_block.non_modal_command) {
    case NON_MODAL_SET_COORDINATE_DATA:  
      // [G10 Errors]: L missing and is not 2 or 20. P word missing. (Negative P value done.)
      // [G10 L2 Errors]: R word NOT SUPPORTED. P value not 0 to nCoordSys(max 9). Axis words missing.
      // [G10 L20 Errors]: P must be 0 to nCoordSys(max 9). Axis words missing.
      if (!axis_words) { FAIL(STATUS_GCODE_NO_AXIS_WORDS) }; // [No axis words]
      if (bit_isfalse(value_words,((1<<WORD_P)|(1<<WORD_L)))) { FAIL(STATUS_GCODE_VALUE_WORD_MISSING); } // [P/L word missing]
      coord_select = trunc(gc_block.values.p); // Convert p value to int.
      if (coord_select > N_COORDINATE_SYSTEM) { FAIL(STATUS_GCODE_UNSUPPORTED_COORD_SYS); } // [Greater than N sys]
      if (gc_block.values.l != 20) {
        if (gc_block.values.l == 2) {
          if (bit_istrue(value_words,bit(WORD_R))) { FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); } // [G10 L2 R not supported]
        } else { FAIL(STATUS_GCODE_UNSUPPORTED_COMMAND); } // [Unsupported L]
      }
      bit_false(value_words,(bit(WORD_L)|bit(WORD_P)));
      
      // Determine coordinate system to change and try to load from EEPROM.
      if (coord_select > 0) { coord_select--; } // Adjust P1-P6 index to EEPROM coordinate data indexing.
      else { coord_select = gc_block.modal.coord_select; } // Index P0 as the active coordinate system
      if (!settings_read_coord_data(coord_select,parameter_data)) { FAIL(STATUS_SETTING_READ_FAIL); } // [EEPROM read fail]
    
      // Pre-calculate the coordinate data changes. NOTE: Uses parameter_data since coordinate_data may be in use by G54-59.
      for (idx=0; idx<N_AXIS; idx++) { // Axes indices are consistent, so loop may be used.
        // Update axes defined only in block. Always in machine coordinates. Can change non-active system.
        if (bit_istrue(axis_words,bit(idx)) ) {
          if (gc_block.values.l == 20) {
            // L20: Update coordinate system axis at current position (with modifiers) with programmed value
            parameter_data[idx] = gc_state.position[idx]-gc_state.coord_offset[idx]-gc_block.values.xyz[idx];
            if (idx == TOOL_LENGTH_OFFSET_AXIS) { parameter_data[idx] -= gc_state.tool_length_offset; }
          } else {
            // L2: Update coordinate system axis to programmed value.
            parameter_data[idx] = gc_block.values.xyz[idx]; 
          }
        }
      }
      break;
    case NON_MODAL_SET_COORDINATE_OFFSET:
      // [G92 Errors]: No axis words.
      if (!axis_words) { FAIL(STATUS_GCODE_NO_AXIS_WORDS); } // [No axis words]
    
      // Update axes defined only in block. Offsets current system to defined value. Does not update when
      // active coordinate system is selected, but is still active unless G92.1 disables it. 
      for (idx=0; idx<N_AXIS; idx++) { // Axes indices are consistent, so loop may be used.
        if (bit_istrue(axis_words,bit(idx)) ) {
          gc_block.values.xyz[idx] = gc_state.position[idx]-coordinate_data[idx]-gc_block.values.xyz[idx];
          if (idx == TOOL_LENGTH_OFFSET_AXIS) { gc_block.values.xyz[idx] -= gc_state.tool_length_offset; }
        } else {
          gc_block.values.xyz[idx] = gc_state.coord_offset[idx];
        }
      }
      break;
      
    default:

      // At this point, the rest of the explicit axis commands treat the axis values as the traditional
      // target position with the coordinate system offsets, G92 offsets, absolute override, and distance
      // modes applied. This includes the motion mode commands. We can now pre-compute the target position.
      // NOTE: Tool offsets may be appended to these conversions when/if this feature is added.
      if (axis_command != AXIS_COMMAND_TOOL_LENGTH_OFFSET ) { // TLO block any axis command.
        if (axis_words) {
          for (idx=0; idx<N_AXIS; idx++) { // Axes indices are consistent, so loop may be used to save flash space.
            if ( bit_isfalse(axis_words,bit(idx)) ) {
              gc_block.values.xyz[idx] = gc_state.position[idx]; // No axis word in block. Keep same axis position.
            } else {
              // Update specified value according to distance mode or ignore if absolute override is active.
              // NOTE: G53 is never active with G28/30 since they are in the same modal group.
              if (gc_block.non_modal_command != NON_MODAL_ABSOLUTE_OVERRIDE) {
                // Apply coordinate offsets based on distance mode.
                if (gc_block.modal.distance == DISTANCE_MODE_ABSOLUTE) {
                  gc_block.values.xyz[idx] += coordinate_data[idx] + gc_state.coord_offset[idx];
                  if (idx == TOOL_LENGTH_OFFSET_AXIS) { gc_block.values.xyz[idx] += gc_state.tool_length_offset; }
                } else {  // Incremental mode
                  gc_block.values.xyz[idx] += gc_state.position[idx];
                }
              }
            }
          }
        }
      }
          
      // Check remaining non-modal commands for errors.
      switch (gc_block.non_modal_command) {        
        case NON_MODAL_GO_HOME_0: 
          // [G28 Errors]: Cutter compensation is enabled. 
          // Retreive G28 go-home position data (in machine coordinates) from EEPROM
          if (!axis_words) { axis_command = AXIS_COMMAND_NONE; } // Set to none if no intermediate motion.
          if (!settings_read_coord_data(SETTING_INDEX_G28,parameter_data)) { FAIL(STATUS_SETTING_READ_FAIL); }
          break;
        case NON_MODAL_GO_HOME_1:
          // [G30 Errors]: Cutter compensation is enabled. 
          // Retreive G30 go-home position data (in machine coordinates) from EEPROM
          if (!axis_words) { axis_command = AXIS_COMMAND_NONE; } // Set to none if no intermediate motion.
          if (!settings_read_coord_data(SETTING_INDEX_G30,parameter_data)) { FAIL(STATUS_SETTING_READ_FAIL); }
          break;
        case NON_MODAL_SET_HOME_0: case NON_MODAL_SET_HOME_1:
          // [G28.1/30.1 Errors]: Cutter compensation is enabled. 
          // NOTE: If axis words are passed here, they are interpreted as an implicit motion mode.
          break;
        case NON_MODAL_RESET_COORDINATE_OFFSET: 
          // NOTE: If axis words are passed here, they are interpreted as an implicit motion mode.
          break;
        case NON_MODAL_ABSOLUTE_OVERRIDE:
          // [G53 Errors]: G0 and G1 are not active. Cutter compensation is enabled.
          // NOTE: All explicit axis word commands are in this modal group. So no implicit check necessary.
          if (!(gc_block.modal.motion == MOTION_MODE_SEEK || gc_block.modal.motion == MOTION_MODE_LINEAR)) {
            FAIL(STATUS_GCODE_G53_INVALID_MOTION_MODE); // [G53 G0/1 not active]
          }
          break;
      }
  }
      
  // [20. Motion modes ]: 
  if (gc_block.modal.motion == MOTION_MODE_NONE) {
    // [G80 Errors]: Axis word exist and are not used by a non-modal command.
    if ((axis_words) && (axis_command != AXIS_COMMAND_NON_MODAL)) { 
      FAIL(STATUS_GCODE_AXIS_WORDS_EXIST); // [No axis words allowed]
    }

  // Check remaining motion modes, if axis word are implicit (exist and not used by G10/28/30/92), or 
  // was explicitly commanded in the g-code block.
  } else if ( axis_command == AXIS_COMMAND_MOTION_MODE ) {
  
    if (gc_block.modal.motion == MOTION_MODE_SEEK) {
      // [G0 Errors]: Axis letter not configured or without real value (done.)
      // Axis words are optional. If missing, set axis command flag to ignore execution.
      if (!axis_words) { axis_command = AXIS_COMMAND_NONE; }

    // All remaining motion modes (all but G0 and G80), require a valid feed rate value. In units per mm mode,
    // the value must be positive. In inverse time mode, a positive value must be passed with each block.
    } else {      
      // Check if feed rate is defined for the motion modes that require it.
      if (gc_block.values.f == 0.0) { FAIL(STATUS_GCODE_UNDEFINED_FEED_RATE); } // [Feed rate undefined]
     
      switch (gc_block.modal.motion) {
        case MOTION_MODE_LINEAR: 
          // [G1 Errors]: Feed rate undefined. Axis letter not configured or without real value.
          // Axis words are optional. If missing, set axis command flag to ignore execution.
          if (!axis_words) { axis_command = AXIS_COMMAND_NONE; }

          break;
        case MOTION_MODE_CW_ARC: case MOTION_MODE_CCW_ARC:
          // [G2/3 Errors All-Modes]: Feed rate undefined.
          // [G2/3 Radius-Mode Errors]: No axis words in selected plane. Target point is same as current.
          // [G2/3 Offset-Mode Errors]: No axis words and/or offsets in selected plane. The radius to the current 
          //   point and the radius to the target point differs more than 0.002mm (EMC def. 0.5mm OR 0.005mm and 0.1% radius).   
          // [G2/3 Full-Circle-Mode Errors]: NOT SUPPORTED. Axis words exist. No offsets programmed. P must be an integer.        
          // NOTE: Both radius and offsets are required for arc tracing and are pre-computed with the error-checking.
        
          if (!axis_words) { FAIL(STATUS_GCODE_NO_AXIS_WORDS); } // [No axis words]
          if (!(axis_words & (bit(axis_0)|bit(axis_1)))) { FAIL(STATUS_GCODE_NO_AXIS_WORDS_IN_PLANE); } // [No axis words in plane]
        
          // Calculate the change in position along each selected axis
          float x,y;
          x = gc_block.values.xyz[axis_0]-gc_state.position[axis_0]; // Delta x between current position and target
          y = gc_block.values.xyz[axis_1]-gc_state.position[axis_1]; // Delta y between current position and target

          if (value_words & bit(WORD_R)) { // Arc Radius Mode  
            bit_false(value_words,bit(WORD_R));
            if (gc_check_same_position(gc_state.position, gc_block.values.xyz)) { FAIL(STATUS_GCODE_INVALID_TARGET); } // [Invalid target]
          
            // Convert radius value to proper units.
            if (gc_block.modal.units == UNITS_MODE_INCHES) { gc_block.values.r *= MM_PER_INCH; }
           
            float h_x2_div_d = 4.0 * gc_block.values.r*gc_block.values.r - x*x - y*y;

            if (h_x2_div_d < 0) { FAIL(STATUS_GCODE_ARC_RADIUS_ERROR); } // [Arc radius error]
    
            // Finish computing h_x2_div_d.
            h_x2_div_d = -sqrt(h_x2_div_d)/hypot_f(x,y); // == -(h * 2 / d)
            // Invert the sign of h_x2_div_d if the circle is counter clockwise (see sketch below)
            if (gc_block.modal.motion == MOTION_MODE_CCW_ARC) { h_x2_div_d = -h_x2_div_d; }  

                           
            */  
            // Negative R is g-code-alese for "I want a circle with more than 180 degrees of travel" (go figure!), 
            // even though it is advised against ever generating such circles in a single line of g-code. By 
            // inverting the sign of h_x2_div_d the center of the circles is placed on the opposite side of the line of
            // travel and thus we get the unadvisably long arcs as prescribed.
            if (gc_block.values.r < 0) { 
                h_x2_div_d = -h_x2_div_d; 
                gc_block.values.r = -gc_block.values.r; // Finished with r. Set to positive for mc_arc
            }        
            // Complete the operation by calculating the actual center of the arc
            gc_block.values.ijk[axis_0] = 0.5*(x-(y*h_x2_div_d));
            gc_block.values.ijk[axis_1] = 0.5*(y+(x*h_x2_div_d));
          
          } else { // Arc Center Format Offset Mode  
            if (!(ijk_words & (bit(axis_0)|bit(axis_1)))) { FAIL(STATUS_GCODE_NO_OFFSETS_IN_PLANE); } // [No offsets in plane]
            bit_false(value_words,(bit(WORD_I)|bit(WORD_J)|bit(WORD_K)));  
          
            // Convert IJK values to proper units.
            if (gc_block.modal.units == UNITS_MODE_INCHES) {
              for (idx=0; idx<N_AXIS; idx++) { // Axes indices are consistent, so loop may be used to save flash space.
                if (ijk_words & bit(idx)) { gc_block.values.ijk[idx] *= MM_PER_INCH; }
              }
            }         

            // Arc radius from center to target
            x -= gc_block.values.ijk[axis_0]; // Delta x between circle center and target
            y -= gc_block.values.ijk[axis_1]; // Delta y between circle center and target
            float target_r = hypot_f(x,y); 

            // Compute arc radius for mc_arc. Defined from current location to center.
            gc_block.values.r = hypot_f(gc_block.values.ijk[axis_0], gc_block.values.ijk[axis_1]); 
            
            // Compute difference between current location and target radii for final error-checks.
            float delta_r = fabs(target_r-gc_block.values.r);
            if (delta_r > 0.005) { 
              if (delta_r > 0.5) { FAIL(STATUS_GCODE_INVALID_TARGET); } // [Arc definition error] > 0.5mm
              if (delta_r > (0.001*gc_block.values.r)) { FAIL(STATUS_GCODE_INVALID_TARGET); } // [Arc definition error] > 0.005mm AND 0.1% radius
            }
          }
          break;
        case MOTION_MODE_PROBE_TOWARD: case MOTION_MODE_PROBE_TOWARD_NO_ERROR:
        case MOTION_MODE_PROBE_AWAY: case MOTION_MODE_PROBE_AWAY_NO_ERROR:
          // [G38 Errors]: Target is same current. No axis words. Cutter compensation is enabled. Feed rate
          //   is undefined. Probe is triggered. NOTE: Probe check moved to probe cycle. Instead of returning
          //   an error, it issues an alarm to prevent further motion to the probe. It's also done there to 
          //   allow the planner buffer to empty and move off the probe trigger before another probing cycle.
          if (!axis_words) { FAIL(STATUS_GCODE_NO_AXIS_WORDS); } // [No axis words]
          if (gc_check_same_position(gc_state.position, gc_block.values.xyz)) { FAIL(STATUS_GCODE_INVALID_TARGET); } // [Invalid target]
          break;
      } 
    }
  }
  
  // [21. Program flow ]: No error checks required.

  // [0. Non-specific error-checks]: Complete unused value words check, i.e. IJK used when in arc
  // radius mode, or axis words that aren't used in the block.  
  bit_false(value_words,(bit(WORD_N)|bit(WORD_F)|bit(WORD_S)|bit(WORD_T))); // Remove single-meaning value words. 
  if (axis_command) { bit_false(value_words,(bit(WORD_X)|bit(WORD_Y)|bit(WORD_Z))); } // Remove axis words. 
  if (value_words) { FAIL(STATUS_GCODE_UNUSED_WORDS); } // [Unused words]

   
  /* -------------------------------------------------------------------------------------
     STEP 4: EXECUTE!!
     Assumes that all error-checking has been completed and no failure modes exist. We just
     need to update the state and execute the block according to the order-of-execution.
  */ 
  
  // [0. Non-specific/common error-checks and miscellaneous setup]: 
  gc_state.line_number = gc_block.values.n;
  
  // [1. Comments feedback ]:  NOT SUPPORTED
  
  // [2. Set feed rate mode ]:
  gc_state.modal.feed_rate = gc_block.modal.feed_rate;
  
  // [3. Set feed rate ]:
  gc_state.feed_rate = gc_block.values.f; // Always copy this value. See feed rate error-checking.

  // [4. Set spindle speed ]:
  if (gc_state.spindle_speed != gc_block.values.s) { 
    // Update running spindle only if not in check mode and not already enabled.
    if (gc_state.modal.spindle != SPINDLE_DISABLE) { spindle_run(gc_state.modal.spindle, gc_block.values.s); }
    gc_state.spindle_speed = gc_block.values.s; 
  }
    
  // [5. Select tool ]: NOT SUPPORTED. Only tracks tool value.
  gc_state.tool = gc_block.values.t;

  // [6. Change tool ]: NOT SUPPORTED

  // [7. Spindle control ]:
  if (gc_state.modal.spindle != gc_block.modal.spindle) {
    // Update spindle control and apply spindle speed when enabling it in this block.    
    spindle_run(gc_block.modal.spindle, gc_state.spindle_speed);
    gc_state.modal.spindle = gc_block.modal.spindle;    
  }

  // [8. Coolant control ]:  
  if (gc_state.modal.coolant != gc_block.modal.coolant) {
    coolant_run(gc_block.modal.coolant);
    gc_state.modal.coolant = gc_block.modal.coolant;
  }
  
  // [9. Enable/disable feed rate or spindle overrides ]: NOT SUPPORTED

  // [10. Dwell ]:
  if (gc_block.non_modal_command == NON_MODAL_DWELL) { mc_dwell(gc_block.values.p); }
  
  // [11. Set active plane ]:
  gc_state.modal.plane_select = gc_block.modal.plane_select;  

  // [12. Set length units ]:
  gc_state.modal.units = gc_block.modal.units;

  // [13. Cutter radius compensation ]: G41/42 NOT SUPPORTED
  // gc_state.modal.cutter_comp = gc_block.modal.cutter_comp; // NOTE: Not needed since always disabled.

  // [14. Cutter length compensation ]: G43.1 and G49 supported. G43 NOT SUPPORTED.
  // NOTE: If G43 were supported, its operation wouldn't be any different from G43.1 in terms
  // of execution. The error-checking step would simply load the offset value into the correct
  // axis of the block XYZ value array. 
  if (axis_command == AXIS_COMMAND_TOOL_LENGTH_OFFSET ) { // Indicates a change.
    gc_state.modal.tool_length = gc_block.modal.tool_length;
    if (gc_state.modal.tool_length == TOOL_LENGTH_OFFSET_ENABLE_DYNAMIC) { // G43.1
      gc_state.tool_length_offset = gc_block.values.xyz[TOOL_LENGTH_OFFSET_AXIS];
    } else { // G49
      gc_state.tool_length_offset = 0.0;
    }
  }
  
  // [15. Coordinate system selection ]:
  if (gc_state.modal.coord_select != gc_block.modal.coord_select) {
    gc_state.modal.coord_select = gc_block.modal.coord_select;
    memcpy(gc_state.coord_system,coordinate_data,sizeof(coordinate_data));
  }
  
  // [16. Set path control mode ]: G61.1/G64 NOT SUPPORTED
  // gc_state.modal.control = gc_block.modal.control; // NOTE: Always default.
  
  // [17. Set distance mode ]:
  gc_state.modal.distance = gc_block.modal.distance;
  
  // [18. Set retract mode ]: NOT SUPPORTED
    
  // [19. Go to predefined position, Set G10, or Set axis offsets ]:
  switch(gc_block.non_modal_command) {
    case NON_MODAL_SET_COORDINATE_DATA:    
      settings_write_coord_data(coord_select,parameter_data);
      // Update system coordinate system if currently active.
      if (gc_state.modal.coord_select == coord_select) { memcpy(gc_state.coord_system,parameter_data,sizeof(parameter_data)); }
      break;
    case NON_MODAL_GO_HOME_0: case NON_MODAL_GO_HOME_1: 
      // Move to intermediate position before going home. Obeys current coordinate system and offsets 
      // and absolute and incremental modes.
      if (axis_command) {
        #ifdef USE_LINE_NUMBERS
          mc_line(gc_block.values.xyz, -1.0, false, gc_state.line_number);
        #else
          mc_line(gc_block.values.xyz, -1.0, false);
        #endif
      }
      #ifdef USE_LINE_NUMBERS
        mc_line(parameter_data, -1.0, false, gc_state.line_number); 
      #else
        mc_line(parameter_data, -1.0, false); 
      #endif
      memcpy(gc_state.position, parameter_data, sizeof(parameter_data));
      break;
    case NON_MODAL_SET_HOME_0: 
      settings_write_coord_data(SETTING_INDEX_G28,gc_state.position);
      break;
    case NON_MODAL_SET_HOME_1:
      settings_write_coord_data(SETTING_INDEX_G30,gc_state.position);
      break;
    case NON_MODAL_SET_COORDINATE_OFFSET:
      memcpy(gc_state.coord_offset,gc_block.values.xyz,sizeof(gc_block.values.xyz));
      break;
    case NON_MODAL_RESET_COORDINATE_OFFSET: 
      clear_vector(gc_state.coord_offset); // Disable G92 offsets by zeroing offset vector.
      break;

  
}
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
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
