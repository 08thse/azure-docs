---
title: Understanding virtual RF (vRF) by demodulating Aqua using GNU Radio
description: Learn how to use virtual RF (vRF) instead of a managed modem, to receive a raw RF signal from NASA's Aqua public satellite and process it in GNU Radio.
author: marclichtman
ms.service: orbital
ms.topic: tutorial
ms.custom: ga
ms.date: 04/21/2023
ms.author: marclichtman
# Customer intent: As an Azure Orbital customer I want easy to understand documentation for virtual RF so I don't have to bug the product team to understand how to build my applications.
---

# Tutorial: Understanding virtual RF (vRF) by demodulating Aqua using GNU Radio

In [Tutorial: Downlink data from NASA's Aqua public satellite](downlink-aqua.md), data from Aqua is downlinked using a managed modem, meaning the raw RF signal received from the Aqua satellite is passed through a modem managed by Azure Orbital.  The output of this modem, which is in the form of bytes, is then streamed to the user's VM.  As part of the step [Configure a contact profile for an Aqua downlink mission ](downlink-aqua.md#configure-a-contact-profile-for-an-aqua-downlink-mission) the **Demodulation Configuration** was set to **Aqua Direct Broadcast**, which is what configured the managed modem to demodulate and decode the RF signal received from Aqua.  Using the vRF concept, no managed modem is used, and instead the raw RF signal is sent to the user's VM for processing.  This concept can apply to both the downlink and uplink, but in this tutorial we will examine the downlink process by receiving Aqua and processing the raw RF signal using GNU Radio.

In this guide, you'll learn how to:

> [!div class="checklist"]
> * Understand the limitations and tradeoffs of using vRF.
> * Configure a contact profile using vRF instead of a managed modem.
> * Process the downlinked data from Aqua (in the form of raw RF) using GNU Radio as the modem.

## Prerequisites

- Complete [Tutorial: Downlink data from NASA's Aqua public satellite](downlink-aqua.md) 

## Understanding the limitations and tradeoffs when using vRF

Before we dive into the tutorial, it's important to understand how vRF works and how it compares to using a managed modem.  With a managed modem, the entire physical (PHY) layer occurs within Azure orbital, meaning the RF signal is immediately processed within Azure orbital and the user only receives the bytes produced by the modem.  Using vRF, there is no managed modem, and the raw RF signal is streamed to the user, form the digitizer at the ground station.  This allows the user to run their own modem, or capture the RF signal for storage and later processing.  

Advantages of vRF include the ability to use modems that cannot be shared with Azure Orbital, or being able to run the same RF signal through a modem while trying different parameters to optimize performance.  Due to the nature of raw RF signals, the packet/file size will typically be greater than the bytes contained within that RF signal; usually between 2-10x larger.

Below provides several RF/digitizer-specific details that may be of interest to a vRF user:

### Downlink (receiving a signal from Azure Orbital)

On the downlink side, a DIFI stream is sent to the user's VM by Azure orbital during a pass, and is expected to be captured by the user (e.g., via tcpdump, socat, or directly ingested into a modem).

- Center Frequency specified in Contact Profile
- The signal bandwidth (BW) is set in the Contact Profile.  The sample rate is 1.25\*BW for X-Band and 1.125\*BW for S-Band. 
- 8 bit depth (2 bytes per IQ sample) 
- Gain mode is automatic
- AGC power target of -10 dBFS
- No spectral inversion is used
- No frequency offset is used
- User VM MTU size should be set to 3650, as this is the max packet size coming from Azure Orbital. 

### Uplink (providing a signal for Azure Orbital to transmit)

On the uplink side, the user must provide a DIFI stream to Azure orbital throughout the pass.

- Center Frequency specified in Contact Profile
- The signal sample rate is set through the DIFI stream (even though a bandwidth is provided as part of the Contact Profile, it's purely for network configuration under the hood).
- Bit depth is set through the DIFI stream but Azure Orbital expects 8 bits
- DIFI stream ID should be set to 0
- MTU size should be 1500 for S-Band and up to 3650 for X-Band
- No spectral inversion is used
- No frequency offset is used

## Step 1: Use AOGS to schedule a contact and collect Aqua data

Execute steps listed in [Tutorial: Downlink data from NASA's Aqua public satellite](downlink-aqua.md) but during step [Configure a contact profile for an Aqua downlink mission ](downlink-aqua.md#configure-a-contact-profile-for-an-aqua-downlink-mission) leave the **Demodulation Configuration** blank and choose UDP for **Protocol**. Lastly, towards the end of the tutorial, instead of the `socat` command (which captures TCP packets), run `sudo tcpdump -i eth0 port 56001 -vvv -p -w /tmp/aqua.pcap` to capture the UDP packets.

> [!NOTE]
> The following three modifications are needed to [Tutorial: Downlink data from NASA's Aqua public satellite](downlink-aqua.md):
>
>   - **Demodulation Configuration**: leave blank
>   - **Protocol**: choose UDP
>   - **Step 8 and 9**: instead use the command `sudo tcpdump -i eth0 port 56001 -vvv -p -w /tmp/aqua.pcap`

After the pass you should have a file `/tmp/aqua.pcap` of size 10-20 GB (depending on the max elevation).  This file contains [DIFI](https://github.com/DIFI-Consortium/DIFI-Certification/blob/main/DIFI_101_Tutorial.md) packets containing the raw RF signal received by the ground station, in the form of IQ samples.  

## Step 2: Extract the IQ samples from the DIFI Packets

The following steps can be performed on the same VM that ran tcpdump, to extract the IQ samples from the DIFI packets.

```
cd ~
git clone https://github.com/DIFI-Consortium/DIFI-Certification.git
cd DIFI-Certification/DIFI_Validator
export DIFI_RX_MODE=pcap
export PCAP_FILE=/tmp/aqua.pcap
export SAVE_IQ=true
python drx.py
```

You should see activity in the terminal if it worked, and there should be a new file `/tmp/samples.iq` growing larger as the script runs.  This new file is a binary IQ file, containing the raw RF signal (this script is essentially stripping off the DIFI header and concatenating many packets worth of IQ samples).  Processing the entire pcap will take a while, but you can feel free to stop it after ~10 seconds, which should save more than enough IQ samples the next step.

## Step 3: Demodulate the Aqua signal in GNU Radio

   :::image type="content" source="media/gnuradio_logo.png" alt-text="GNU Radio logo" lightbox="media/gnuradio_logo.png":::

GNU Radio is a free & open-source software development toolkit that provides signal processing blocks and many example digital signal processing (DSP) applications. It can be used with readily-available low-cost RF hardware to create software-defined radios, or without hardware in a simulation-like environment. It is widely used in research, industry, academia, government, and hobbyist environments to support both wireless communications research and real-world radio systems.  We will be using GNU Radio to demodulate Aqua (i.e., GNU Radio will be acting as the modem).

Although GNU Radio can be used in headless mode, in this tutorial we will be using the GUI, so you will need to have `/tmp/samples.iq` on a VM with X11 forwarding or computer with Ubuntu 20/22 desktop (e.g., copied to a host computer using `scp`).

### Installing GNU Radio

If you're using Ubuntu 22 simply run `sudo apt-get install gnuradio`.  If instead you are on Ubuntu 20 then use the following commands:
```
sudo add-apt-repository ppa:gnuradio/gnuradio-releases
sudo apt-get update
sudo apt-get install gnuradio python3-packaging
```
Check that GNU Radio installed and that graphics are working using `gnuradio-companion`; a window should pop up.  

### Running the Aqua flowgraph

A GNU Radio application is called a "flowgraph", and it typically either processes or generates an RF signal.  The flowgraph we will be using can be [downloaded here](https://gist.githubusercontent.com/777arc/e5824993f1f55f890bb99ab4453db42b/raw/b523d4ae61a21436d58796ab0026f8d510d3ba7b/aqua.grc).  Open this .grc file within GNU Radio and you should see the following flowgraph: 

   :::image type="content" source="media/aqua_flowgraph.png" alt-text="GNU Radio Aqua flowgraph" lightbox="media/aqua_flowgraph.png":::

Verify that your `/tmp/samples.iq` exists (or if you saved it somewhere else, double click the File Source block and update the path), then click the play button at the top to run the flowgraph.  If the previous steps were successful, and your Aqua contact was a success, you should see the following power spectral density (PSD):

   :::image type="content" source="media/aqua_psd.png" alt-text="GNU Radio Aqua PSD" lightbox="media/aqua_psd.png":::

If no GUI showed up then check GNU Radio's output in the bottom right.  If the GUI shows up but resembles a flat line, it means the contact didn't actually recieve the Aqua signal, double check that auto-track is enabled in your Contact Profile and that the center frequency was entered correctly.

The time it takes GNU Radio to finish will be based on how long you let `drx.py` run combined with your computer/VM CPU power.  As the flowgraph runs, it is demodulating the RF signal in `/tmp/samples.iq` and creating the file `/tmp/aqua_out.bin` which contains the raw bytes within the signal.

We will end this tutorial here, although if you are interested in decoding the bytes into imagery, you can either use [NASA's tools](satellite-imagery-with-orbital-ground-station.md#step-2-install-nasa-drl-tools) or open source tools such as [altillimity/X-Band-Decoders](https://github.com/altillimity/X-Band-Decoders).  The goal of this tutorial was to show how vRF can be received from Azure Orbital, and we simply used GNU Radio as an example vRF modem for the downlink.

## Next steps

To easily deploy downstream components necessary to receive and process spaceborne earth observation data using Azure Orbital Ground Station, see:
- [Azure Orbital Integration](https://github.com/Azure/azure-orbital-integration)