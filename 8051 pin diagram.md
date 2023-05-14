## pin diagram of 8051 microcontroller















































![[Pasted image 20230504204414.png | 500]]

---

![[Pasted image 20230504232800.png | 1000]]

- $XTAL_1, XTAL_2$ crystal oscillator pins
	- will be connected to a quartz crystal
	- as the plates between which the crystal is placed get charged the crystal starts to oscillate i.e. 1 0 1 0 1 0
	- this is called as a clock pulse / trigger.
	- we need a clock to trigger the processor to execute the operations one by one.
	- higher the frequency, higher is the speed.
	- 8051 operates at 12MHz frequency.

- $Reset \ pin$ 
	- this signal resets the whole system.
	- on reset, the whole power supply is withdrawn and given back.
	- RAM will lose all its information (data) and the default values will be loaded by the processor.
	- ROM information will be retained. (code/program)
	- on reset, the processor executes a program called BIOS/monitor program which is used to initialize everything in the system.

- $ALE \ pin$ address latch enable
	- 8051 has 16 bit address bus $(A_0 - A_{15}) => (A_0 - A_7) \ Port_0 \ and \ (A_8 - A_{15}) \ Port_2$
		- the $(A_0 - A_7)$ are multiplexed and can carry either address or data $(AD_0 - AD_7)$.
	- so $Port_0$ is multiplexed to carry both address and data. 
		- to indicate that $(A_0 - A_7)$ carries address, the 8051$\mu C$  will set $ALE = 1$
		- to indicate that $(A_0 - A_7)$ carries data, the 8051$\mu C$ will clear $ALE = 0$

- $\bar{EA} \ pin$ external access of ROM
	- if we want more program memory we can connect external ROM
		- unlike RAM, the programmer can use only internal ROM, only external ROM or a mix of both,
			- **only internal ROM:** lets say the 4kB address space will be $0h - FFFh$ $(\bar{EA} = 1)$
			- **mix of both:** the address of external ROM will continue from where the address of internal ROM ends. 
				- i.e. $4kB + 32kB = 0h-FFFh \ and \ 1000h-8FFFh$ $(\bar{EA} = 1)$
			- **only external ROM:** lets say the 32kB address space will be $0h - 7FFFh$ $(\bar{EA} = 0)$
	- **we can connect either external RAM $(upto \ 64kB)$ or external ROM $(upto \ 64kB)$ or even both at the same time! (made possible coz of separate control pins for ROM and RAM, $\bar{PSEN}$ for external ROM and $\bar{WR}\ and \ \bar{RD}$ for external RAM)**
	- external memory shall be connected to $Port0$ and $Port2$
	- if we want more data memory we can connect external RAM
		- internal RAM and external RAM are both operated separately and do not interfere with each other unlike ROM.
		- internal RAM is always used irrespective of the external RAM.
		- in fact there are 2 separate instructions in 8051 to move data,
			- for internal RAM, instruction MOV is used
			- for external ROM, instruction MOVX is used

- $Vcc \ pin$ 
	- connects to $5V$ power supply

- $GND \ pin$
	- connects to the ground

- $P_0, P_1, P_2, P_3$ bidirectional 8 bits io ports
	- $P_{0.0} - P_{0.7}$ / $AD_0 - AD_7$
		- io operations and also can be multiplexed as data bus or lower order address bus
		- multiplexed as address bus, data bus only when an external memory is attached to the $\mu C$
	- $P_{1.0} - P_{1.7}$
		- io operations only
	- $P_{2.0} - P_{2.7}$ / $A_8-A_{15}$
		- io operations and also as higher order address bus
		-  used as address bus only when an external memory is attached to the $\mu C$
	- $P_{3.0} - P_{3.7}$ / $alternate\ func\ of\ P_3$
		- io operations and has other alternate functionalities
	- all the ports are bit addressable.

- $alternate \ functions \ of \ Port3$
	- serial port
		- $P_{3.0} \ (RXD)$ - 
		- $P_{3.1} \ (TXD)$ - 
	- external hardware interrupts
		- $P_{3.2} \ (\bar{INT0})$ - 
		- $P_{3.3} \ (\bar{INT1})$ - 
	- timer clock inputs
		- timer hardware needs a clock to operate. it can be either the main clock of the processor with the frequency downsized or dedicated external clock.
		- but for 8051 the frequency is 12MHz which 12 million cycles per second, but since the timer is 16bit and the max is 65535 cycles which will take less than a second to complete. even if we downsized it to 1MHz it is still 1 million cycles per second.
		- since for real world we need delays in seconds, minutes or even hours, we may need a clock with lesser operating frequency based on the requirement.
		- $P_{3.4} \ (T0)$ - external clock for timer 0
		- $P_{3.5} \ (T1)$ - external clock for timer 1
		- if no clock is connected, downsized main clock 1MHz is used.
	- write and read probe
		- these are control signals from the processor indicating whether it needs to read or write. they act as output pins.
		- these control pins come into picture only when external RAM is used. for external ROM, $\bar{PSEN}$ is used.
		- $P_{3.6} \ (\bar{WR})$ - if the processor wants to write data into external RAM then $\bar{WR}$ is cleared 
		- $P_{3.7} \ (\bar{RD})$ -  if the processor wants to read data from external RAM then $\bar{RD}$ is cleared
		- **note:** when external memory is connected, 
			- $P_0$ and $P_2$ will act as lower order address/data bus and higher order address bus respectively.
			- $P_{3.6}$ and $P_{3.7}$ will act as write and read control signals.
			- so these pins could not be used for io operations.

- $\bar{PSEN} \ pin$ program status enable
	- this control pin comes into picture only when external ROM is used.
	- if the processor wants to fetch/read the program code from external ROM, then the $\bar{PSEN}$ bit is cleared.
	- **note:** external RAM and external ROM are separately controlled by $\bar{WR} \ and \ \bar{RD}$ and $\bar{PSEN}$ respectively, this opens up the option to connect $64kB\ external\ ROM\ (0h-FFFFh)$ and $64kB\ external\ RAM\ (0h-FFFFh)$ at the same time.
	- because of these separate control pins for RAM and ROM in 8051, even in 16 bit address space, two slabs of 64kB of memory (128kB in total) could be connected and used.


## question to summarize the working on 8051
---
**an external RAM is connected to 8051 $\mu C$. if the processor needs to read value 25 from the address location 4000h in the external RAM. what will be the sequence of events?**

![[Pasted image 20230505010406.png | 500]]
**sequence**
1. the processor will initially give the **address** 4000h ($A_8 - A_{15} = 40h\ (P_2), A_0 - A_7 = 00h\ (P_0)$ ) and the $ALE = 1$. 
2. but (say) 4000h is present in both external RAM as well as external ROM so the processor will give $\bar{RD}=0$ **control signal** to select the external RAM.
3. the $ALE = 0$ and the processor will then read the value 25 in 4000h from the **data bus** $(D_0 - D_7) = 25\ (P_0)$ 

**alternately, if the processor wants to read the value at address 4000h in external ROM then step 2 would be,**
2. the processor will give $\bar{PSEN} = 0$ **control signal** to select external ROM.


#queries_8051_pin_diagram 
## queries
---
- [ ] how does the 8051 $\mu C$ know whether an external ROM or RAM is connected to it by the programmer? are there any special registers the programmer needs to set in order to inform the processor?