# Firmware notes

Inspecting the Boss-provided firmware image version 1.07, available from Boss's website.

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
