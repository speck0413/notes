Mirror Spica+ Firmware Interaction architecture
    Write arguments to fixed location
    Write "GO" bit at fixed register
    Wait for "DONE" bit at fixed register
    Per operation (INIT, ENTER_OP, etc..)

No requirement to re-download Firmware
    INIT + ENTER_OP should allow any mode i.e.
    examples
        firmware download
        INIT + ENTER_OP for DUAL PRBS 53G
        test BER
        then INIT + ENTER_OP EGRESS 56G
        test BER

Internal Loopbacks Discussion?
    can we do internal loopback line *before* the drivers and have sufficient coverage (design question)
    we will have internal host loopback
package id bumps instead of fuses?
thermal diode bumps