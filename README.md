# Basic Tool Command Language Scripts: <br>
## Authored by Ramya, ECE student at BMSCE, passionate about tool automation, chip design, and making scripts do the heavy lifting.
This repository contains a curated set of TCL scripts written to simulate common tasks in VLSI design automation, inspired by real tool flows like Synopsys PrimeTime and Design Compiler.<br>
Scripts automate processes like slack analysis, area/power reporting, batch simulations, clock constraint generation, and more — all with beginner-friendly, readable TCL logic.<br>
Ideal for EDA beginners, STA learners, and anyone looking to get hands-on with scripting in a VLSI context.<br>



# 1.A mini script to simulate checking timing for multiple modules

set modules [list alu control datapath uart]

### Simulate some fake slack values
set slack_values [list 0.25 -0.12 0.05 0.00]

### Create a report file
set fp [open "timing_report.txt" "w"]
puts $fp "Timing Report"
puts $fp "--------------"

### Loop through modules and write timing info
for {set i 0} {$i < [llength $modules]} {incr i} {
    set mod [lindex $modules $i]
    set slack [lindex $slack_values $i]
    
    if {[expr $slack < 0]} {
        set status "VIOLATED"
    } else {
        set status "PASS"
    }

    puts $fp [format "%-10s : Slack = %5.2f ns → %s" $mod $slack $status]
}

close $fp
puts "Timing report generated!"

# 2.Renaming report files with timestamps
Imagine you're running a flow, and every time you generate a new report, you want it to be saved with the current date/time so it doesn't overwrite the old one.

💻 TCL Script: Auto-Rename Report with Timestamp
#### Get current time as YYYYMMDD_HHMM
set time_stamp [clock format [clock seconds] -format "%Y%m%d_%H%M"]

#### Create a report name using the timestamp
set report_name "timing_report_$time_stamp.txt"

#### Open the file for writing
set fp [open $report_name "w"]

#### Sample content
puts $fp "Timing Report Generated on: $time_stamp"
puts $fp "-----------------------------------"

#### Dummy module list
set mods [list alu fifo uart]

#### Write dummy slack values
foreach mod $mods {
    set slack [expr rand() * 0.3 - 0.1]  ;
#### Random slack between -0.1 and +0.2
    if {[expr $slack < 0]} {
        set status "VIOLATED"
    } else {
        set status "PASS"
    }
    puts $fp [format "%-8s : Slack = %5.2f ns → %s" $mod $slack $status]
}

#### Close the file
close $fp

#### Print result
puts "Report saved as: $report_name"

# 3.Module Area Summary (TCL only, tool-style)

## This script:
### Loops through modules,assigns fake area values,prints a clean area report.

#### List of modules (block names)
set modules [list alu decoder mux register memory]

#### Assign random area values to each module (between 500 and 3000 units)
puts "Module Area Report"
puts "-------------------"

foreach mod $modules {
    set area [expr int(rand() * 2500 + 500)]  ;
#### random int between 500–3000
#### Categorize by area size
    if {$area > 2500} {
        set tag "XL"
    } elseif {$area > 1500} {
        set tag "L"
    } elseif {$area > 1000} {
        set tag "M"
    } else {
        set tag "S"
    }

#### Print result
    puts [format "%-10s : Area = %4d units → Size: %s" $mod $area $tag]
}

# 4.Power Report Generator
#### List of modules
set modules [list alu decoder mux register memory]

#### Print report header
puts "Module Power Report"
puts "--------------------"

#### Loop through each module
foreach mod $modules {
#### Generate random power between 0.05 and 2.5
    set power [expr rand() * 2.45 + 0.05]

#### Categorize power level
    if {$power < 0.5} {
        set level "Low"
    } elseif {$power < 1.5} {
        set level "Medium"
    } else {
        set level "High"
    }

#### Print formatted result
    puts [format "%-10s : Power = %4.2f W → %s" $mod $power $level]
}


#### Sample Output:
#### Module Power Report
--------------------
alu        : Power =  0.42 W → Low  <br> 
decoder    : Power =  1.36 W → Medium  <br> 
mux        : Power =  2.22 W → High<br> 
register   : Power =  1.01 W → Medium<br> 
memory     : Power =  0.17 W → Low<br> 

# 5.Voltage source names
set lines [list VDD1 VDD2 VDD3 VDD4 VDD5]

puts "Voltage Monitor Report"
puts "------------------------"

foreach line $lines {
#### Generate random voltage between 0.5V and 1.5V
    set voltage [expr rand() + 0.5]

#### Decide status
    if {$voltage < 0.9} {
        set status "Under-voltage"
    } elseif {$voltage <= 1.2} {
        set status "Normal"
    } else {
        set status "Over-voltage"
    }

#### Print result
    puts [format "%-5s : %.3f V → %s" $line $voltage $status]
}

Output:
Voltage Monitor Report
------------------------
VDD1  : 0.812 V → Under-voltage <br> 
VDD2  : 1.153 V → Normal <br> 
VDD3  : 1.391 V → Over-voltage <br> 
VDD4  : 1.008 V → Normal <br> 
VDD5  : 0.743 V → Under-voltage <br> 


# 6.Log Parser Script
## GOAL:
Read a .rpt file, look for specific words (like “VIOLATED”) and print those lines. <br>
💻 Script:
#### Sets the filename to a variable
set log "timing_report.txt" 

#### Opens the file in read mode
set fp [open $log r]

####  Reads the file content, splits it line-by-line into a list
set lines [split [read $fp] "\n"]

#### Closes the file 
close $fp

#### Prints a header to make output clean
puts "Violations Found:"
foreach line $lines {
    if {[string match *VIOLATED* $line]} {
        puts $line
    }
}
 #### Loops through each line
 If the line contains the word “VIOLATED” → print it!
#### Used in: PrimeTime, DC, Questa logs

# 7.Constraint Generator Script (SDC)

## GOAL:
Auto-generate clock constraints for multiple ports. <br>
💻 Script:
#### List of all clock ports in your design
set clk_ports [list clk1 clk2]
foreach clk $clk_ports {
    puts "create_clock -name $clk -period 10 [get_ports $clk]"
}
#### For each clock:Create a command that tools like PrimeTime use
#### Generates this:
create_clock -name clk1 -period 10 [get_ports clk1]
create_clock -name clk2 -period 10 [get_ports clk2]
#### Used to automate writing .sdc files in STA/Synthesis


### 🎯 WHY IS THIS USED? <br>
i.This script auto-generates clock constraints for all clock ports.<br>
ii.In the VLSI flow, you don’t just write Verilog and simulate it.<br>
 You also need to tell the tools:<br>
“Hey, this port is a clock.”<br>
“Its period is 10 ns.”<br>
“Connect this constraint to this port.”<br>
This is done using an SDC file (Synopsys Design Constraints).<br>

#### So this script is used when:
You have many clocks (10+ clk ports)
You don’t want to write create_clock lines manually <br>
You want to avoid typos or inconsistent period values<br>
You’re working in a team and need repeatable constraints across runs<br>

#### Real Industry Scenario:
Imagine you’re in a company and your chip has:
clk_sys, clk_usb, clk_audio, clk_uart, clk_camera<br>

#### Instead of writing:<br>
create_clock -name clk_sys -period 10 [get_ports clk_sys]<br>
create_clock -name clk_usb -period 10 [get_ports clk_usb]<br>
...

#### You use:
set clks [list clk_sys clk_usb clk_audio clk_uart clk_camera]<br>
foreach clk $clks {<br>
    puts "create_clock -name $clk -period 10 [get_ports $clk]"<br>
}
💥 BOOM — all 5 lines generated in 1 loop!



