## VSDBabySoC PVT Corner Analysis (Post-Synthesis Timing)
The following TCL script can be executed to perform STA for the available PVT corners using the Sky130 timing libraries.
The timing libraries can be downloaded from:

  https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd/tree/master/timing

  git clone this :

  ```bash
  git clone https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd.git
  ```
  then get into this path :

  ```bash
  cd examples
  cd BabySoC
  cd skywater-pdk-libs-sky130_fd_sc_hd/timing
  ```
This script below can be used to perform Static Timing Analysis (STA) across all PVT corners for which the Sky130 Liberty files are provided,
create a file sta_across_pvt.tcl and paste it 
```
# ================================================
# OpenSTA Multi-Lib Timing Analysis Script
# Project: VSDBabySoC
# ================================================

# 1. Define Liberty Library List
set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

# 2. Read PLL and DAC Liberty Files (adjusted path)
read_liberty /mnt/vsdbabysoc/src/lib/avsdpll.lib
read_liberty /mnt/vsdbabysoc/src/lib/avsddac.lib

# 3. Run STA for each library
for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {
    puts "\n========================================"
    puts "Running STA with $list_of_lib_files($i)"
    puts "========================================\n"

    read_liberty /mnt/opensta/examples/BabySoC/skywater-pdk-libs-sky130_fd_sc_hd/timing/$list_of_lib_files($i)

    read_verilog /mnt/vsdbabysoc/output/synthesized/vsdbabysoc.synth.v

    link_design vsdbabysoc
    current_design

    read_sdc /mnt/vsdbabysoc/src/sdc/vsdbabysoc_synthesis.sdc
    check_setup -verbose

    report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} \
        > /mnt/opensta/out/min_max_$list_of_lib_files($i).txt

    exec echo "$list_of_lib_files($i)" >> /mnt/opensta/out/sta_worst_max_slack.txt
    report_worst_slack -max -digits {4} >> /mnt/opensta/out/sta_worst_max_slack.txt

    exec echo "$list_of_lib_files($i)" >> /mnt/opensta/out/sta_worst_min_slack.txt
    report_worst_slack -min -digits {4} >> /mnt/opensta/out/sta_worst_min_slack.txt

    exec echo "$list_of_lib_files($i)" >> /mnt/opensta/out/sta_tns.txt
    report_tns -digits {4} >> /mnt/opensta/out/sta_tns.txt

    exec echo "$list_of_lib_files($i)" >> /mnt/opensta/out/sta_wns.txt
    report_wns -digits {4} >> /mnt/opensta/out/sta_wns.txt
}
```

Running the script :
```
docker run -it   -v /home/manav-sheth/OpenSTA:/mnt/opensta   -v /home/manav-sheth/VLSI/VSDBabySoC:/mnt/vsdbabysoc   opensta   -exit /mnt/opensta/examples/BabySoC/sta_across_pvt.tcl
```
ERROR : This could be solved by setting up the input and output delay ports properly at vsdbabysoc_synthesis.sdc


paste this this file vsdbabysoc_synthesis.sdc in babysoc 

```bash
set_units -time ns
create_clock -name clk -period 11 [get_pins {pll/CLK}]
set_max_delay 10 -from [get_clocks clk] -to [get_ports {OUT}]
set_input_delay -clock clk -max 2.0 [get_ports {ENb_CP ENb_VCO REF VCO_IN VREFH reset}]
set_output_delay -clock clk -max 2.0 [get_ports {OUT}]
set_false_path -to [get_ports {OUT}]
```





```
docker run -it   -v /home/manav-sheth/OpenSTA:/mnt/opensta   -v /home/manav-sheth/VLSI/VSDBabySoC:/mnt/vsdbabysoc   opensta   -exit /mnt/opensta/examples/BabySoC/sta_across_pvt.tcl
```
