% Marvell xDSP Test List
% Stephen Peck; Kris Hublitz
% August 12, 2023


# Today's Test List

## PAM4 Without MultiLane
* Firmware Download Successful
* Setup Successful
* PRBS Lock
* PRBS LSB BER
* PRBS MSB BER
* PRBS Signal to Noise Ratio

## PAM4 With MultiLane
### Critical Metrics
* Firmware Download Successful
* Setup Successful
* SNDR - Industry Standard PAM4, NRZ uses Jitter
* Eye Height(s)
* Eye Width(s)
* RLM94
### Informational Metrics
* Eye Voltage Levels (zero, one, two and three)
* Eye Min and Max Voltage
* Peak to Peak Voltage
* Eye Base and Top
* Eye Amplitude


# Loopback Options
## Dual PRBS
```mermaid
flowchart LR
    prbs_gen1[PRBS Generator Host]
    prbs_gen2[PRBS Generator Line]
    prbs_check1[PRBS Checker Host]
    prbs_check2[PRBS Checker Line]

    host_tx[Host TX]
    host_rx[Host RX]
    line_tx[Line TX]
    line_rx[Line RX]

    prbs_gen1 -->|Internal| host_tx
    host_tx -->|External| host_rx
    host_rx -->|Internal| prbs_check1
    
    prbs_gen2 -->|Internal| line_tx
    line_tx -->|External| line_rx
    line_rx -->|Internal| prbs_check2
```

## Egress or Host to Line
```mermaid
flowchart LR
    prbs_gen[PRBS Generator]
    prbs_check[PRBS Checker]

    host_tx[Host TX]
    host_rx[Host RX]
    line_tx[Line TX]
    line_rx[Line RX]

    prbs_gen -->|Internal| host_tx
    host_tx -->|External| host_rx
    host_rx -->|Internal| line_tx
    line_tx -->|External| line_rx
    line_rx -->|Internal| prbs_check
```

## Ingress or Line to Host
```mermaid
flowchart LR
    prbs_gen[PRBS Generator]
    prbs_check[PRBS Checker]

    host_tx[Host TX]
    host_rx[Host RX]
    line_tx[Line TX]
    line_rx[Line RX]

    prbs_gen -->|Internal| line_tx
    line_tx -->|External| line_rx
    line_rx -->|Internal| host_tx
    host_tx -->|External| host_rx
    host_rx -->|Internal| prbs_check
```

# Desired Support for Future Efforts

- Masking for each eye
- Offline capability with waveform playback for regression testing
- Reduction in overhead for adjusting CW Tone Output

# MultiLane Hardware Configuration

```mermaid
flowchart LR
    prbs_gen[PRBS Generator]
    prbs_check[PRBS Checker]

    host_tx[Host TX]
    host_rx[Host RX]
    line_tx[Line TX]
    line_rx[Line RX]

    prbs_gen -->|Internal| host_tx
    host_tx -->|External| host_rx
    host_rx -->|Internal| prbs_check

    line_tx --> multilane_dso[MultiLane DSO]
    multilane_bert[MultiLane BERT] --> line_rx
```

---

# Tester Network Configuration

```mermaid
flowchart TD
    host[Host PC]
    subgraph Tester Chassis
        c_switch[Chassis Switch]
        LinkScale
        g_switch[Garage Backplane Switch]
        DSO1
        BERT1
        DSO2
        BERT2
    end
    host <--> c_switch
    c_switch <--> LinkScale
    c_switch <--> g_switch

    g_switch <--> DSO1
    g_switch <--> BERT1
    g_switch <--> DSO2
    g_switch <--> BERT2
```

```mermaid
flowchart TD
    subgraph Tester Chassis
        LinkScale <--> c_switch[Chassis Switch]
        c_switch <--> g_switch[Garage Backplane Switch]
        g_switch <--> DSO1
        g_switch <--> BERT1
        g_switch <--> DSO2
        g_switch <--> BERT2
    end
```