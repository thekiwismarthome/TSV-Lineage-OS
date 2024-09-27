# TSV-Lineage-OS
Install Lineage OS 15.1 on a Lenovo Thinksmart View

# Installation Instructions on RPi3b+ (Ubuntu Server 20.05)

## Prerequisites
- **RPi3b+** running **Ubuntu Server 20.05**
- **USB2 port** must be used (does not work with USB3)

## Resources
- [Lineage 15.1 Installation Guide](https://github.com/pgale/lineage_15.1_Installation_TSV)
- Followed discussion from [here](https://github.com/pgale/lineage_15.1_Installation_TSV/discussions/19)

## Steps

1. Update the path to your `%USERNAME%`:
    ```bash
    export PATH=$PATH:/home/%USERNAME%/.local/bin
    ```

2. Add the necessary repository and install Python 3.9 along with other dependencies:
    ```bash
    sudo add-apt-repository ppa:deadsnakes/ppa
    sudo apt update
    sudo apt install pip python3.9 python3.9-venv python3-dev python3-pip unzip
    ```

3. Set up a virtual environment:
    ```bash
    mkdir ~/.venvs
    python3.9 -m venv ~/.venvs/myvenv
    ```

4. Install additional dependencies:
    ```bash
    sudo apt install pip python3.9 python3.9-venv python3.9-dev python3-pip python3-docopt python3-usb unzip
    ```

5. Install `pip` and other packages (from [this StackOverflow thread](https://stackoverflow.com/a/65644846)):
    ```bash
    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
    python3.9 get-pip.py
    pip3.9 install gdown
    ```

6. Disable and remove `ModemManager`:
    ```bash
    sudo systemctl stop ModemManager && sudo systemctl disable ModemManager && sudo apt purge ModemManager
    ```

7. Clone the EDL repository:
    ```bash
    git clone https://github.com/bkerler/edl.git
    ```

8. Build EDL:
    ```bash
    cd edl
    git submodule update --init --recursive
    sudo python3.9 setup.py build
    cd ~
    ```

9. Create a directory for the Lenovo ThinkSmart View files and download the necessary files:
    ```bash
    mkdir ThinkSmartView
    wget s3.wasabisys.com/filestash-buk/lenovo-thinksmart-view/CD-18781Y.200628.084.zip
    unzip -j ~/CD-18781Y.200628.084.zip "CD-18781Y.200628.084/image/*" -d ~/ThinkSmartView/image
    ```

10. Download additional required images:
    ```bash
    cd ThinkSmartView/
    wget s3.us-west-1.wasabisys.com/rom-release/LineageOS/15.1/starfire/lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img
    gdown 1m34JjwxukdH8Y5WEUCZuPSiyrQHrI6ke
    wget sourceforge.net/projects/opengapps/files/arm/20220215/open_gapps-arm-8.1-pico-20220215.zip
    ```

11. Install EDL requirements:
    ```bash
    cd ~/edl
    pip3.9 install -r requirements.txt
    cd ../
    ```

12. Flash the firmware using EDL:
    ```bash
    sudo ~/edl/edl qfil ~/ThinkSmartView/image/rawprogram_unsparse.xml ~/ThinkSmartView/image/patch0.xml ~/ThinkSmartView/image --loader=/home/%USERNAME%/ThinkSmartView/image/prog_emmc_firehose_8953_ddr.mbn
    sudo ~/edl/edl w aboot ~/ThinkSmartView/image/emmc_appsboot.mbn
    sudo ~/edl/edl w abootbak ~/ThinkSmartView/image/emmc_appsboot.mbn
    sudo ~/edl/edl w recovery ~/ThinkSmartView/lineage-15.1-20240531-UNOFFICIAL-starfire-recovery.img
    ```

13. Install ADB:
    ```bash
    sudo apt install adb
    ```

## Booting into Recovery Mode

1. Boot the ThinkSmart View (TSV) into recovery mode:
    - Remove power, hold **Vol+**, and power back on.
    - Release the **Vol+** button when the Lenovo splash screen appears.

2. LineageOS recovery screen should appear.

3. Use **Vol+/-** buttons to highlight **Apply update** and slide the camera shutter to select.

4. Select **Apply from ADB** (default selection); ADB Sideload will be displayed.

## Sideloading the LineageOS Image and OpenGapps

1. Sideload LineageOS image from the connected computer:
    ```bash
    adb sideload ~/ThinkSmartView/lineage-15.1-20240602-UNOFFICIAL-starfire.zip
    ```

2. Sideload OpenGapps package:
    Follow steps 9 and 10 above to place TSV in ADB Sideload mode, then:
    ```bash
    adb sideload ~/ThinkSmartView/open_gapps-arm-8.1-pico-20220215.zip
    ```


Sideload LineageOS image from the connected computer:
adb sideload ~/ThinkSmartView/lineage-15.1-20240602-UNOFFICIAL-starfire.zip

Sideload OpenGapps package. Follow steps 9 and 10 above to place TSV in ADB Sideload, then:
adb sideload ~/ThinkSmartView/open_gapps-arm-8.1-pico-20220215.zip
