## [Download latest release](https://github.com/ledoge/jxr_to_png/releases/latest/download/release.zip)
([Visual C++ Redistributable](https://aka.ms/vs/17/release/vc_redist.x64.exe) required, which you likely already have installed)

# About
This is a simple command line tool for converting HDR JPEG XR files, such as Windows HDR screenshots, to PNG.

By default, the output format is 16-bit PNG with BT.2100 + PQ color space, with the actual data quantized to 10 bits to keep the file size reasonably low. The files should display properly in any Chromium-based browser (including Electron apps like Discord).

If the `sdr` flag is passed, the tool converts the linear JXR image to standard SDR, clipping at 100 nits, and outputs a 16-bit sRGB PNG with full 16-bit precision.

# Notes:

This app will scale the luminance of the HDR JXR image by 2.03 and encode to PNG PQ BT2020. This is because apps like Chrome and Lightroom normalise HDR photo exposure by dividing by 203 nits, then multiplying by device SDR brightness (I use 100 nits).

# Usage
```
jxr_to_png input.jxr [output.png] [scale_factor] [sdr]
```
or auto-generating the output filename with a scale factor or SDR flag:
```
jxr_to_png input.jxr [scale_factor] [sdr]
```

### Examples:
- **No scaling:**
  ```
  jxr_to_png input.jxr
  ```
  *(Generates `input.png`)*

- **SDR conversion:**
  ```
  jxr_to_png input.jxr sdr
  ```
  *(Converts the linear JXR image to standard SDR, clipping at 100 nits, and auto-generates `input_sdr.png`)*

- **Downscale by 2x (e.g. 4K to 1080p):**
  ```
  jxr_to_png input.jxr 2
  ```
  *(Auto-generates `input_1080p.png` with suffix representing the scaled height)*

- **Downscale by 2x and convert to SDR:**
  ```
  jxr_to_png input.jxr 2 sdr
  ```
  *(Downscales by 2x, converts to SDR, and auto-generates `input_1080p_sdr.png`)*

- **Downscale by 1.5x (e.g. 4K to 1440p) to a custom output name:**
  ```
  jxr_to_png input.jxr output.png 1.5
  ```

Instead of using the command line, you can also drag a .jxr file onto the executable (uses default 1x scaling).

# HDR metadata
The MaxCLL value is calculated as suggested in the paper [On the Calculation and Usage of HDR Static Content Metadata](https://doi.org/10.5594/JMI.2021.3090176), by taking the light level of the 99.99 percentile brightest pixel. This is an underestimate of the "real" MaxCLL value calculated according to H.274, so it technically causes some clipping when tone mapping. However, following the spec can lead to a much higher MaxCLL value, which causes e.g. Chromium's tone mapping to significantly dim the entire image, so this trade-off seems to be worth it.
