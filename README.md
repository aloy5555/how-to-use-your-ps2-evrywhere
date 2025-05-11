# PS2 Streaming Setup Guide

This guide provides a step-by-step tutorial on setting up your PlayStation 2 (PS2) to display its output in a web browser on any device. This includes using HDMI adapters, a capture card, and streaming software.

---

## What You’ll Need

### Hardware:
- **PlayStation 2 console**
- **PS2-to-HDMI adapter**
- **HDMI cable**
- **HDMI capture card** (e.g., Elgato HD60 S, or an affordable USB capture card)
- **A computer with USB ports**
- **Stable internet connection** (if streaming online)

### Software:
- **[OBS Studio](https://obsproject.com/)**: For video capture and streaming.
- **[Node.js](https://nodejs.org/)**: For serving the video feed via HTTP.
- Optionally, a **local or public video server** (for remote access).

---

## Step 1: Connect Your PS2 to Your Computer

### A. Convert PS2 Output to HDMI
1. Plug the **PS2-to-HDMI adapter** into the AV Multi-Out port on your PS2.
2. Connect an **HDMI cable** from the adapter to the HDMI input on your capture card.

### B. Connect the Capture Card to Your Computer
1. Plug the **capture card** into your computer via USB.
2. Ensure the capture card is detected by your operating system.

---

## Step 2: Configure OBS Studio

### Install OBS Studio:
1. Download and install **OBS Studio** from [here](https://obsproject.com/).

### Add a Video Capture Source:
1. Open OBS Studio.
2. Click the "+" button under **Sources** and select **Video Capture Device**.
3. Name the source (e.g., "PS2 Capture") and click **OK**.
4. Select your **capture card** from the **Device** dropdown menu.

### Adjust Video Settings:
1. Set the resolution (e.g., 720x480 for PS2 games).
2. Ensure the frame rate is appropriate (e.g., 30 FPS for most PS2 games).

### Test the Feed:
1. Turn on your PS2, and you should see its video output in OBS Studio.
2. Resize or crop the video as needed.

---

## Step 3: Serve the Video Feed Locally Using Node.js

To make the video accessible in a web browser, you can serve it over HTTP.

### A. Install Node.js
1. Download and install **Node.js** from [here](https://nodejs.org/).
2. Verify installation by running:
    ```bash
    node -v
    ```

### B. Create a Simple HTTP Server
1. Create a new directory (e.g., `ps2-stream`) and navigate to it:
    ```bash
    mkdir ps2-stream
    cd ps2-stream
    ```
2. Create a new file called `server.js`:
    ````javascript name=server.js
    const http = require('http');
    const fs = require('fs');
    const path = require('path');

    const PORT = 8080;

    http.createServer((req, res) => {
        const filePath = path.join(__dirname, 'output.mp4'); // Replace with your OBS output file
        const stat = fs.statSync(filePath);

        res.writeHead(200, {
            'Content-Type': 'video/mp4',
            'Content-Length': stat.size,
        });

        const stream = fs.createReadStream(filePath);
        stream.pipe(res);
    }).listen(PORT, () => {
        console.log(`Server running at http://localhost:${PORT}/`);
    });
    ````
3. Save the file and run the server:
    ```bash
    node server.js
    ```
4. Open your browser and navigate to `http://localhost:8080/`. You should see your PS2 video feed.

---

## Step 4: Stream to Other Devices

### A. Find Your Local IP Address
1. Run the following command to find your IP:
    ```bash
    ipconfig # Windows
    ifconfig # macOS/Linux
    ```
2. Note your local IP address (e.g., `192.168.1.100`).

### B. Access the Feed on Another Device
- Instead of `http://localhost:8080/`, use `http://<your-local-ip>:8080/` (e.g., `http://192.168.1.100:8080/`).

### C. Port Forwarding for Remote Access
1. Log in to your router’s admin panel.
2. Set up **port forwarding** to forward traffic on port `8080` to your computer’s local IP.
3. Use your public IP address (e.g., `http://<your-public-ip>:8080/`) to access the stream from anywhere.

---
