```tcl

set ns [new Simulator]
$ns rtproto DV
set nf [open out.nam w]
$ns namtrace-all $nf
set tf [open tracefile.tr w]
$ns trace-all $tf
for {set a 0}{$a < 2}{incr a}{
set f$a [open out$a.tr w]
}
proc finish {} {
global ns nf tf f0 f1
$ns flush-trace
close $nf
close $tf
close $f0
close $f1
exec xgraph out0.tr out1.tr -geometry 800x400 &
exit 0
}
for{set i 0} {$$i <6} {incr i}{
set n$i [$ns node]
}
$ns duplex-link $n0 $n2 2Mb 10ms DropTail
$ns duplex-link $n1 $n2 2Mb 10ms DropTail
$ns duplex-link $n2 $n3 1.7Mb 20ms DropTail

$ns duplex-link $n2 $n4 2.7Mb 20ms DropTail
$ns duplex-link $n4 $n3 1.7Mb 20ms DropTail
$ns duplex-link $n3 $n5 1.7Mb 20ms DropTail
$ns queue-limit $n2 $n3 10
#nam configuration can be ignored
$ns color 1 Blue
$ns color 2 Red
set tcp [new Agent/TCP]
$ns attach-agent $n0 $tcp
set sink [new Agent/TCPSink]
$ns attach-agent $n5 $sink
$ns connect $tcp $sink
$tcp set fid_ 1
$ns attach-agent $n0 $tcp
set ftp0 [new Application/FTP]
$ftp0 attach-agent $tcp
$ftp set type_ FTP
set udp [new Agent/UDP]
$ns attach-agent $n1 $udp
set null [new Agent/LossMonitor]
$ns attach-agent $n5 $null
$ns connect $udp $null
$udp set fid_ 2
set cbr [new Application/Traffic/CBR]
$cbr attach-agent $udp
$cbr set type_ cbr
$cbr set rate_ 1mb
$cbr set random_ false
$cbr set packet_size_ 1000

roc record {} {
global sink null f0 f1
#Get an instance of the simulator
set ns [Simulator instance]
#Set the time after which the procedure should be called again
set time 0.5
#How many bytes have been received by the traffic sinks?
set bw0 [$sink set bytes_]
set bw1 [$null set bytes_]
#Get the current time
set now [$ns now]
#Calculate the bandwidth (in MBit/s) and write it to the files
puts $f0 "$now [expr $bw0/$time*8/1000000]"
puts $f1 "$now [expr $bw1/$time*8/1000000]"
#Reset the bytes_ values on the traffic sinks
$sink set bytes_ 0
$null set bytes_ 0
#Re-schedule the procedure
$ns at [expr $now+$time] "record"
}
$ns at 0.4 "$ftp0 start"
$ns at 0.5 "$cbr start"
$ns at 14.0 "$ftp0 stop"
$ns at 14.5 "$cbr stop"
$ns rtmodel-at 9.0 down $n2 $n3
$ns rtmodel-at 12.5 up $n2 $n3
$ns at 0.0 "record"
$ns at 15.0 "finish"
$ns run

```