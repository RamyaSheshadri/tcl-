# tcl-
## 1.A mini script to simulate checking timing for multiple modules

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
    set slack [expr rand() * 0.3 - 0.1]  ;# Random slack between -0.1 and +0.2
    if {[expr $slack < 0]} {
        set status "VIOLATED"
    } else {
        set status "PASS"
    }
    puts $fp [format "%-8s : Slack = %5.2f ns → %s" $mod $slack $status]
}

#### Close the file
close $fp

#### Print success msg
puts "✅ Report saved as: $report_name"


