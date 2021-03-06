SSRV is a main framework to build a high performance CPU core. If it is connected with system control units as the same as peripherals, it will provide the high-performance attribute for different CPU cores.
 
Since the simulation environment is inherited from SCR1, it is very convenient to instantiate SSRV into SCR1 to improve instruction throughput ability. SSRV will replace the basic instruction processing modules.  All instructions will be dealt with except system and CSR instructions, which will be forwarded to SCR1’s system and CSR module: scr1_pipe_csr.sv.

![ssrv-on-scr1](https://github.com/risclite/SuperScalar-RISCV-CPU/blob/master/wiki/png/ssrv-on-scr1.png)

SSRV and "scr1_pipe_csr" constitute a new basic hierarchy level: "pipeline". The "pipeline" level could be instantiated by the upper level: "core", which has an accessorial module: "scr1_reset_cells". The top hierarchy level adds more functions: 64KB TCM, a 64-bit timer and AXI4/AHB-lite bus interfaces.

Compared with the original SCR1 core, the DHRY score will be improved from 1.14 DMIPS/MHz to 1.87 DMIPS/MHz. Unfortunately, the instruction bus width is fixed to 32 bits by SCR1, which is a limitation of performance. It is obvious that SSRV could not issue 2 or 3 instructions simultaneously when only 1 instruction is fetched from the source memory.

The directory "ssrv-on-scr1" is on the subject of this SSRV CPU core based on SCR1. It has two sub-directories:

*  sim/

    It likes the simulation environment of SSRV, but the difference is that the instruction bus bit width is fixed to 32 bit and could not be adjusted to other values. 

    The simulation could be switched from SSRV to SCR1 by commenting the verilog defination "USE_SSRV" of "rtl/define_para.v".

    *  rtl/
    
    "ssrv_pipe_top.sv" instantiates "ssrv_top" and " scr1_pipe_csr". It is the top module of SSRV CPU core. You can treat "rtl/" as a whole set of SSRV CPU core.

    *  scr1/
    
    This directory is inherited from SCR1, which provides a whole simulation package.

        build/ --- compiled hex/elf/dump files

        src/ --- source files of SCR1, which includes RTL and testbench files.

        sim/ --- where to start simulation and compile work library.

    Just enter sim/scr1/sim/ and run "compile.do" and "sim.do". 


* fpga/

    The FPGA demonstration project is based on Altera DE2-115 development board. 

    * DE2-115/ 
    
    The main directory of this FPGA project. Open " DE2_115.qsf" file to find more details. DE2-115.v is the top module.

    * pll/
    
    A PLL ip generated by "MegaWizard Plug-In Manager", which will turn a 50 MHz clock to a 30 MHz clock.
    
    * ram/
    
    A 64KB dual-port ram generated by "MegaWizard Plug-In Manager", which contains a mif file: code.mif. This mif file is converted from build/ dhrystone21_imc_noinline.hex.

    * rtl/ --- The SSRV CPU core.

    * scr1/ --- SCR1 source files.

    * test/

    Two synthesizable files: rxtx.v, which is a simple UART verilog file; and "ssrv_memory.v, which instantiates the 64KB dualport ram.
    
    The FPGA board should be connected with a UART terminal, which has a configuration of 9600, even, 1 stop bit. You can change the baud rate through the parameters of rxtx.v. Please open DE2-115.v, find the instantiation of rxtx and give your "mhz" and "baud".

    
