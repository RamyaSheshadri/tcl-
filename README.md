# tcl-
# 1.A mini script to simulate checking timing for multiple modules

set modules [list alu control datapath uart]

# Simulate some fake slack values
set slack_values [list 0.25 -0.12 0.05 0.00]

# Create a report file
set fp [open "timing_report.txt" "w"]
puts $fp "Timing Report"
puts $fp "--------------"

# Loop through modules and write timing info
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
