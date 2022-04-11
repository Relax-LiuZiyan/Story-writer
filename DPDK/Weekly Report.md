---
title: Weekly Report
tags: 新建
renderNumberedHeading: true
grammar_cjkRuby: true
---
# 2022-04-11 DPDK-Pktgen Usage issues
## DPDK-Pktgen Description
DPDK-Pktgen is a high performance packet sending tool implemented by a third party based on the DPDK platform. It can realize the problem of multi-stream sending very well, just need to configure the corresponding core in the cfg file. 

The installation of DPDK-Pktgen is a tedious task, as it requires lua and pacp library files, which need to be downloaded and compiled separately, as well as adapted to the DPDK version. For detailed steps, please refer to the chapter on dpdk Pktgen in the DPDK Learning Record.

``` bash?linenums
 description = 'A Pktgen default simple configuration'

 # Setup configuration
 setup = {
     'exec': (
     'sudo', '-E'
         ),

     'devices': (
	 	# Location of NIC on PCIE
         '2d:00.0', '2d:00.1'  #
         ),
     # UIO module type, igb_uio, vfio-pci or uio_pci_generic
     'uio': 'igb_uio'
     }

 # Run command and options
 run = {
     'exec': (
     #'sudo', '-E', 'LD_LIBRARY_PATH=%(sdk)s/%(target)s/lib/x86_64-native-linux-gcc'
	 # Location of the DPDK compiler library
	'sudo', '-E', 'LD_LIBRARY_PATH=%(sdk)s/%(target)s/lib' 
         ),
     # Application name and use app_path to help locate the app
     'app_name': 'pktgen',

     # using (sdk) or (target) for specific variables
     # add (app_name) of the application
     # Each path is tested for the application
     'app_path': (
         './app/%(target)s/%(app_name)s',
         '%(sdk)s/%(target)s/app/%(app_name)s',
         './build/app/%(app_name)s',
         ),

     'cores': '3,4-5,6-7',
     'nrank': '4',
     'proc': 'auto',
     'log': '7',
     'prefix': 'pg',

     'blacklist': (
         #'03:00.0', '05:00.0',
         #'81:00.0', '84:00.0'
         ),
     'whitelist': (
         #'05:00.0,safe-mode-support=1',
         #'84:00.0,safe-mode-support=1',
         #'03:00.0', '81:00.0'
         ),

     'opts': (
         '-v',
         '-T',
         '-P',
         '-j',
         ),
     'map': (
         '[4:5].0',
         '[6:7].1',
         ),

     'theme': 'themes/black-yellow.theme'
     }

```