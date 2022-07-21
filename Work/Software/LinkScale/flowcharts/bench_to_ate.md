# ATE Control from Bench GUI, PS1600 vs Link Scale

This gives an overview of the high level architecture for controlling ATE from Bench EVK Software. The architecture does not fundamentally change, but throughput is increased.

<hr><br><br>

# General Notes

Note Link-Scale is capable of a 20MHz clock with an effective throughput of 15Mbps. The throughput does not take into account any overhead of the protocol and is the wire throughput.

The PS1600https://marvell.zoom.us/j/98134566431?pwd=YWJHb2lEaUZwakZtc0o0UXlNakZsUT09 is implemented in a way that a reg_set/reg_get can be performed sequentially. The XMLRPC enables support for \*\_list which populates a list of addresses and data for performing the operation, the PS1600 implementation will do this sequentially which means there isn't a savings or throughput increase. Link Scale's architecture makes support of variable length transactions much more straightforwards, so a throughput bump is experienced by these commands.

<hr><br><br>

# Today's Flow using XML-RPC ~1.8mS to 2.4mS per reg_get/reg_set

```mermaid
sequenceDiagram
    participant GUI as Design GUI <br> Windows
    participant ATE as ATE PC
    participant DIG_CARD as PS1600

    Note over GUI,FTDI: Test case on GUI, bit-banging on PS1600.
    Note over GUI,ATE: Requests made per reg_get/reg_set
    rect rgba(128, 128, 128, 0.1)
        Note over GUI,FTDI: XML RPC Request to ATE Host Server
        GUI->>ATE: Request (~200uS)
        ATE->>DIG_CARD: MDIO Bit-Bang (1.4mS - 2mS)

        Note over GUI,FTDI: XML RPC Response from ATE Host Server
        DIG_CARD->>ATE: Response
        ATE->>GUI: Response (~200uS)
    end


```

<hr><br><br>

# XMLRPC Flow with LinkScale ~880uS per reg_get/reg_set

```mermaid
sequenceDiagram
    participant GUI as Design GUI <br> Windows
    participant ATE as ATE PC
    participant LINKSCALE as Link Scale
    participant FTDI as FTDI

    Note over GUI,FTDI: Test case on GUI, bit-banging on Link Scale FTDI
    Note over GUI,ATE: Requests made per reg_get/reg_set
    rect rgba(128, 128, 128, 0.1)
        Note over GUI,FTDI: XML RPC Request to LINKSCALE Server
        GUI->>ATE: Request (~200uS)
        ATE->>LINKSCALE: Request (~200uS)
        LINKSCALE->>FTDI: MDIO Bit-Bang (~80uS)

        Note over GUI,FTDI: XML RPC Response from LINKSCALE Server
        FTDI->>LINKSCALE: Response
        LINKSCALE->>ATE: Response (~200uS)
        ATE->>GUI: Response (~200uS)
    end

```

<hr><br><br>

# Native API Flow on LinkScale ~100uS per reg_get/reg_set

Code is running natively on Link-Scale.

```mermaid

sequenceDiagram
    participant TEST_CASE as Python or C/C++ Test Case
    participant FTDI_API as Ftdi API
    participant FTDI as FTDI

    Note over TEST_CASE,FTDI: Test case from GUI running natively on Link Scale
    Note over TEST_CASE,FTDI_API: Requests made per reg_get/reg_set
    rect rgba(128, 128, 128, 0.1)
        Note over TEST_CASE,FTDI: API Request Made
        TEST_CASE->>FTDI_API: FTDI request via API (~20uS)
        FTDI_API->>FTDI: MDIO Bit-Bang (~80uS)

        Note over TEST_CASE,FTDI: API Returns Value
        FTDI->>FTDI_API: Response
        FTDI_API->>TEST_CASE: Response
    end

```

<hr><br><br>
