# Firmware notes

Inspecting the Boss-provided firmware image version 1.07, available from Boss's website.

## Hunt for the start vector

The reference manual states that the image consists of:

1. Image vector table (IVT),
2. Boot data,
3. Device configuration data,
4. User code + data.

The IVT header was located at a 4096 bytes offset, found to hold the following:

* u8:  magic tag 0xd1
* u16: IVT big-ending length, 0x0020
* u8:  magic version, 0x41
* u32: address of 1st instruction, little-endian 0x11250060.

The first three fields are precisely what the reference manual states should be there. The pointer
points at a 0x2511 byte offset into the 0x60000000 area, memory mapped to the firmware image.

```shell
$ arm-none-eabi-objdump -D -b binary -marm --start-address=9489 --stop-address=9505 rc10rrom.bin
rc10rrom.bin:     file format binary

Disassembly of section .data:

00002511 <.data+0x2511>:
    2511:	064806b6 			; <UNDEFINED> instruction: 0x064806b6
    2515:	0a600149 	beq	0x1802a41
    2519:	08f38268 	ldmeq	r3!, {r3, r5, r6, r9, pc}^
    251d:	80480588 	subhi	r0, r8, r8, lsl #11
```

TODO: What's going on here? Figure out why the start instruction is not recognized by the
disassembler.

## Strings

Looking for strings in the firmware image yields interesting results:

```shell
$ strings rc10rrom.bin | fgrep .cpp | uniq
task_frame\task_frame.cpp
sys\force_updater.cpp
task_frame\task_frame_evtsys.cpp
task_frame\task_frame.cpp
sys\task_system.cpp
sys\msg_container_queue.cpp
sys\main.cpp
drv\lcd_drv.cpp
panel\ezhmi\ez_hmi_msc.cpp
panel\ezhmi\ez_hmi_menu.cpp
panel\ezhmi\ez_hmi_page.cpp
panel\ezhmi\ez_hmi_msc.cpp
task_frame\task_mgr.cpp
sys\boot_mode.cpp
sys\task_misc.cpp
sys\main.cpp
drv\lcd_drv.cpp
```
* The "task frame" parts appears to originate from
[Cpp-Taskflow](https://github.com/cpp-taskflow/cpp-taskflow). The task graphs illustrated there
looks similar to the state charts in the RC-10R user manual, so perhaps Boss are implementing the
pedal functionality as stateful taskflows.
* "ezhmi" likely refers to [some LCD
  panel](https://www.e-gizmo.net/oc/kits%20documents/EZ%20HMI%20display%20terminal/EZHMI_Manual_Rev1r0.pdf)
  manufactured by EZAutomation. The combination of a LCD and a rotary encoder with a push button is
  definitely looking similar to what the RC-10R has. TODO: confirm this by disassembling the RC-10R
  further, looking closer at the I/O board.
* TODO: What about the "sys" parts? If they involve "updating", "message queues" and "boot mode",
  could this be the startup code from Boss? Maybe something generic from the SDK?

The rest of the non-garbage strings found appears related to boot operation, firmware update,
checksum verification, and SD-card/filesystem mounting. In any case, there's no trace of any strings
matching the names of any menu options, like "RHYTHM" or "LOOPING". It means that the CPU is feeding
the LCD that data in some other way. TODO: figure out how.
