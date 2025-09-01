# srsRAN with USRP x310 and COTS UEs

Guide to setup srsRAN with USRP x310 and Google Pixel

## USRP Setup

https://github.com/uoy-research/srsRAN-USRPs-OTA?tab=readme-ov-file#steps-for-setting-up-usrp-x310

## SIM Setup

### Install Dependencies

```bash
sudo apt-get install pcscd pcsc-tools libccid libpcsclite-dev python3-pyscard
```

### Connect SIM Card Reader

1. Connect your SIM card reader to your computer.
2. Insert a programmable SIM card into the reader.

### Verify Connection

Check the status of the connection:

```bash
pcsc_scan
```

If the SIM card reader is recognized, you should see **"Card inserted"**.

---

### Note:

The SIM programming section on the srsRAN website is outdated as of **23 Apr 2025**.

Use the following repository to program the SIM:

- [pysim Repository](https://gitea.osmocom.org/sim-card/pysim)

```bash
cd pysim
```

---

### SIM Programming Commands

1. Check the current ISIM configuration:

   ```bash
   ./pySim-read.py -p0
   ```

2. Program the SIM (replace values accordingly):

   ```bash
   ./pySim-prog.py -p0 -s (ICCID) --mcc=901 --mnc=70 -a (ADM) --imsi=(IMSI)
   ```

   **Example:**

   ```bash
   ./pySim-prog.py -p0 -s 8949440000001343795 --mcc=901 --mnc=70 -a 87641773 --imsi=999700000134379
   ```

- As soon as the programming is done, the **Ki** and **OPC** will be generated automatically. Please note them down as they will be needed while inserting info in the 5g core.

---

### SUCI Configuration via pySIM-shell

```text
./pySim-shell.py -p0
pySIM-shell (MF)> select MF
pySIM-shell (MF)> select ADF.USIM
pySIM-shell (MF/ADF.USIM)> select EF.UST
pySIM-shell (MF)> verify_adm <ADM-KEY>
pySIM-shell (MF/ADF.USIM/EF.UST)> ust_service_deactivate 124
pySIM-shell (MF/ADF.USIM/EF.UST)> ust_service_deactivate 125
```

---

### Phone Setup Instructions

- MCC and MNC in the APN on the phone are auto-filled based on the first 5 digits of the IMSI.
- The srsRAN website uses OnePlus 8T which connects better with roaming (PLMN: 90170).
- For Pixel phones, roaming hacks are usually unnecessary. Use PLMN **00101** (MCC+MNC), and the IMSI must start with **00101**.
- Use the **COTS UE setup steps** from the srsRAN documentation.

#### Configure Your Pixel Phone:

1. Enable developer mode: Tap **Build Number** multiple times in phone settings.
2. Open dialer and enter `*#*#4636#*#*`, then set **Preferred Network Type** to **NR only**.
3. The phone can see signal without SIM registration in the 5G core.

## Starting up the 5g core

- Pick either the dockerised 5g core or the non dockerised 5g core (recommended)

---

## Troubleshooting

### Pixel Disconnection Fix

If the Pixel device keeps disconnecting:

1. Dial `*#*#0702#*#*`
2. Adjust timers:
   - Set `NR_TIMER_WAIT_IMS_REGISTRATION` to `-1` (infinite timeout)
   - Set `SUPPORT_IMS_NR_REGISTRATION_TIMER` to `0` (disable timeout)

These settings are **SIM-specific** and persist across reboots.

---

### Connectivity Test

Once the phone is connected:

- Use **Termux app** on the phone:
  - **Uplink Test**: `ping 10.45.0.1`
- From the 5G core:
  - **Downlink Test**: `ping 10.45.1.2`
