To build SDR++ from source on Ubuntu 24.04 with SoapySDR support for your custom QSD sound card SDR, you need to navigate a few dependency changes specific to Ubuntu 24.04 (Noble Numbat). Most notably, the `libvolk2-dev` package used in older tutorials has been replaced by `libvolk-dev`.

To ensure the build process succeeds without you having to manually disable all the other default hardware modules in CMake, the easiest path is to install the development headers for the default radios alongside the core and SoapySDR packages.

Here are the exact packages and commands you need.

### 1. Install the Required Dependencies

Open your terminal and run the following command to install the build tools, core libraries, and the **SoapySDR** development headers:

```bash
sudo apt update
sudo apt install -y \
    build-essential cmake git \
    libfftw3-dev libglfw3-dev libglew-dev libvolk-dev libzstd-dev \
    libsoapysdr-dev librtaudio-dev portaudio19-dev \
    libairspyhf-dev libairspy-dev libiio-dev libad9361-dev \
    libhackrf-dev librtlsdr-dev libbladerf-dev liblimesuite-dev
```
*(Note: Packages like `librtlsdr-dev` and `libhackrf-dev` are included here purely to satisfy the default CMake configuration so the build doesn't throw errors looking for them.)*

### 2. Build and Install SDR++

Since you have already cloned the repository, open a terminal inside your `SDRPlusPlus` source folder and run the following commands step-by-step:

```bash
# Move into the source directory (if you aren't already there)
cd SDRPlusPlus

# Create a build directory and move into it
mkdir -p build
cd build

# Configure the build system, explicitly telling it to build the SoapySDR module
cmake .. -DOPT_BUILD_SOAPY_SOURCE=ON

# Compile SDR++ using all available CPU cores
make -j$(nproc)

# Install the compiled binaries and modules system-wide
sudo make install

# Refresh shared libraries
sudo ldconfig
```

### 3. Accessing your QSD SDR via SoapyAudio

Once the installation completes, you can launch the software by typing `sdrpp` in the terminal or opening it from your Ubuntu applications menu. 

To use your homebrew QSD SDR:
1. Look at the **Source** panel on the left side of the SDR++ UI.
2. Click the drop-down menu and select **SoapySDR**.
3. In the device selection drop-down below it, your QSD sound card should appear (exposed via your `soapyaudio` driver). 
4. If it doesn't automatically show up, you may need to click the gear icon next to the source drop-down and input your Soapy device arguments (e.g., `driver=audio`) in the **Device Args** field, then refresh.
5. Click **Play** at the top. 

*Tip: Because QSDs essentially feed raw I/Q signals over the Left/Right channels of a stereo sound card, ensure your system audio mixer (like PulseAudio or PipeWire) hasn't muted the input device or applied any mono-downmixing/noise-cancellation filters to the sound card.*
