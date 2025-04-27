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

- The **Ki** and **OPC** will be generated automatically.
