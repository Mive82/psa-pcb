# Description - Peugeot Infotainment Project PCB

This is a repository for the PCB used in the Peugeot Infotainment Project.

It uses <https://github.com/morcibacsi/PSAVanCanBridgeHW> as a base for the hardware.

The main chips used are the TSS463C VAN driver and MCP2551 CAN transciever.

For more information, check out <https://github.com/Mive82/psa-docs>

The goal is to have one PCB. The PCB is supposed to be a RPi shield on which a ESP32 is going to be mounted.

Input voltage: 12 V (car battery, on startup, it can drop to 7 V)
Output voltage: 3.3 V && 5 V

Which devices are going to be powered by this board?
	- RPi 	(dev)
	- ESP32 (dev)
	- some multimedia display?

Multiple power rails, where are they comming from, how do they work?

What are the power requirements?
	Of the whole system: up to 5 A
	For each component:
		- RPi:
		- ESP:

What voltage does the RPi need?

What voltage does the ESP32 need?
	Needs 5 V for the Vin pin as it has a built in voltage regulator (linear). What we could do is not use the DEV board but use the ESP32 processor and then we need to supply it with 3.3 V

	What would be the power saved if we did the before mentioned manuver?
		Ether way, I would need to go from 12 V to 5 V (switching power supply), and then from 5 V to 3.3 V which could be done ether with the linear or with the switching power supply. 

# Repo structure

- images -> folder with project images
	
- KiCad files -> folder with corresponding PCP files/folders
	- Gerber files (mby move inside KiCad files?)
	- lib
	- bom?

- Datasheets -> Datasheets for each used IC
- Documentation -> General project documentation
- docs.pdf	-> General documentation file, placed here so one does not have to enter the /Documentation folder (could be a complete replacement for README.md, problem for the future)
- README.md

# ToDo
	- the ESP should be mounted through headers