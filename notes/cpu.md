# CPU

The CPU is an approximately 10x10mm chip located in the middle of the
main board, covered by a black and white sticker (labelled "A190422"
and in smaller text "65873A190401104") with a QR code on it.
Underneath the sticker, the CPU is labelled "MIMXRT1051DVL6B",
identifying it as a i.MX RT1051 ARM Cortex-M7 processor.

* [RT1050 Processor Reference Manual](../doc/IMXRT1050RM.pdf).
* [RT1050 Data Sheet](../doc/2603846.pdf).

## High-Assurance Boot (HAB) ##

The RT1051 got 96 kB of on-chip ROM (the "boot ROM") containing the
initial bootcode. The job of this bootcode seems to be checking the
efuse-configured boot mode and verifying the firmware image before
jumping to its location and executing from there (optionally
decrypting the image first).

### Bad news ###

The Boss images may be cryptographically signed.

### Good news ###

The Boss images may _not_ be cryptographically signed. I guess I'd
somehow have to either:

1. read the values of the efuses,
2. have it try to run a verified correct image that is not signed by
   Boss. A verified image might be created by replacing some
   instruction somewhere with a NOP or something.

... to know for sure.

The Boss images don't appear encrypted at least, so there's that.

The bootcode not only checks efuses to determine boot mode, but also
GPIO pins (specifically for development purposes according to the
refguide). It may be possible to override the boot mode and make the
RT1051 boot from e.g. USB instead of flash.

No idea yet if arbitrary efuses can be overridden by GPIO, disabling
signature checks altogether.
