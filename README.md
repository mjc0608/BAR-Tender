# BAR-Tender 

Welcome everyone! BAR-Tender is a physical FPGA I/O Device which services physical memory reads/writes via UMDF2 driver. This project is a succesor to [pciworm](http://github.com/defparam/pciworm.git "pciworm") and is enhanced with much more features.

Features:
- Perfect Windows FPGA UMDF2 driver example to fork
- Leverages MSI Interrupts to indicate FPGA operation completion
- Framework and examples written for **Python 3.5** (Ctypes WinAPI Interface)
- Driver interface adaptable to other languages using WinAPI
- Tons of memory functions and kernel access features
- And more...

Limitations:
- A bunch... Targets only Windows 10 x64

# High Level Architecture
<center>

![](https://i.imgur.com/CsX7Erh.png)
</center>

# Python APIs

| Function | Arguments | Description |
| --- | --- | --- |
| BARTender.**fpgaRead64**(addr) | **addr** = physical addr on PCIe BAR of FPGA<br>**Return** = 64-bit data | Reads FPGA MMIO |
| BARTender.**fpgaWrite64**(addr, data) | **addr** = physical addr on PCIe BAR of FPGA<br>**data** = 64-bit data to write<br>**Return** = None | Writes FPGA MMIO |
| BARTender.**memRead64**(addr) | **addr** = Host physical memory address<br>**Return** = 64-bit data | Reads Physical Memory |
| BARTender.**memWrite64**(addr, data) | **addr** = Host physical memory address<br>**data** = 64-bit data to write<br>**Return** = None | Writes Physical Memory |
| BARTender.**vmemRead64**(CR3,addr) | **CR3** = Target process CR3/DirBase<br>**addr** = Process virtual memory address<br>**Return** = 64-bit data | Reads Process Virtual Memory |
| BARTender.**vmemWrite64**(CR3,addr, data) | **CR3** = Target process CR3/DirBase<br>**addr** = Host physical memory address<br>**data** = 64-bit data to write<br>**Return** = None | Writes Process Virtual Memory |
| BARTender.**find_KPCR**() | **Return** = Virtual address of Kernel Processor Control Region Structure | Scans memory for the KPCR pointer and returns it |
| BARTender.**va2pa**(CR3,VADDR) | **CR3** = Target process CR3/DirBase<br> **VADDR** = Virtual address to translate to Physical Address <br>**Return** = Physical Address | Performs a virtual to physical address translation |
| BARTender.**swap_pa**(dirbase,vaddr,new_pa) | **dirbase** = Target process CR3/DirBase<br> **vaddr** = Virtual address to target <br>**new_pa** = New physical address to store in the PTE of this virtual address <br>**Return** = Old Physical Address that was replaces | Performs a physical address swap to a target PTE to a target process |
| BARTender.**get_dirbase**(pid) | **pid** = Target process id<br> **Return** = Dirbase/CR3 of target process id | Scans kernel memory and returns the Dirbase/CR3 for the target PID |

# Driver APIs
Take a look at **BARTender.py** and how it uses Ctypes to communicate to the driver. Also look at **public.h** and **MsgProcessor.cpp** to understand how driver messages are passed via IOCTL.


# Install
This project is completely open-source so feel free to install Vivado and Visual Studio to compile your own binaries. However for those who would like to quickly use the existing binaries, I have releases the FPGA bitstream/drivers/test certificate in the /release area
1. Once you get the PicoEVB you will need to load the BAR-Tender.bit file onto the device. There are many guides that exist on how to do this.
2. In order to install the UMDF2 driver you will need to load the test certificate and then after install the inf file by right clicking and selecting install
3. If you do not trust the certificate and binaries feel free to compile from source!

When Sucessful your BAR-Tender device should look like this in the devive manager:
![](https://pbs.twimg.com/media/DggoIngUEAAigix.jpg)

# Hardware
The FPGA platform used on this project is the [PicoEVB](http://www.picoevb.com/ "PicoEVB"). The PicoEVB is a relatively inexpensive FPGA PCIe card that fits into an M.2 A+E slots. You can purchase your own PicoEVB for **$219** dollars. The hardware specs are as follows:

<center>

| Feature | Specification |
| --- | --- |
| FPGA | Xilinx Artix XC7A50T-2CSG325C |
| Form Factor | M.2 (NGFF), keyed for A and E slots |
| Dimensions | 22x30x3.8 mm |
| Host Interface | PCIe x1 gen 2 (5 Gb/s) |
| Host Tools | Vivado 2017.3 preferred |
| MGT Loopback | Yes |
| Built-in JTAG | Yes |
| External I/O configurations <BR/> via I/O connector | (digital+ differential analog) <BR/> 4+0 <BR/> 2+1 <BR/> 0+2 |
| External I/O via PCIe connector | 4x 3.3V digital I/O (configurable) |
| External MGT connection | 1x MGT via U.FL connectors |
| External clock ref | 1x clkin via U.FL connectors |
| User-controllable LEDs | 3 |


![](https://i.imgur.com/JJrGQGq.png)   </center>


# Example - test1.py
This example tests fpga reads and writes from Host to FPGA, Fpga reads and writes from FPGA to Host (initiated by Host) and dumps the first 0x180 bytes of physical host memory.
![](https://i.imgur.com/ew1DMG1.png)

# Example - test2.py
This example searches for kernel memory structures and dumps all EPROCESS structures
![](https://i.imgur.com/W8P9eXs.png)

# Example - test3.py
This example shows how to use BAR-Tender to perform a PTE pointer swap
![](https://i.imgur.com/5E3rux1.png)