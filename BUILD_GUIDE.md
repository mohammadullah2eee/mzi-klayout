# Build Guide: A Mach–Zehnder Interferometer in KLayout

This guide shows how to build a design similar to the one in this repository. Menu names can differ slightly between SiEPIC-Tools versions, so if a menu is not where this guide says, check the official SiEPIC documentation (links at the end).

## 1. What you need

- A computer with Windows, macOS or Linux
- KLayout (free): https://www.klayout.de
- SiEPIC-Tools and the SiEPIC EBeam PDK (free, installed in step 2)
- Optional: a circuit simulator. SiEPIC-Tools can run simulations from the SiEPIC menu. Check the SiEPIC documentation for the simulation options available in your version.

## 2. Install the tools

1. Install and open KLayout.
2. Go to **Tools → Manage Packages**.
3. Search for **SiEPIC** and install **SiEPIC-Tools** and the **SiEPIC EBeam PDK**.
4. Restart KLayout. A **SiEPIC** menu now appears.

## 3. Start a new layout

1. Create a new layout (**File → New Layout**).
2. Choose the **EBeam** technology.
3. Add a floorplan with the option in the SiEPIC menu so that your design stays inside the allowed chip area.

## 4. Understand the MZI before you draw it

An MZI has an input, a splitter, two arms, a combiner and an output.

- If both arms have the same length, the output depends only on the coupler.
- If one arm is longer by ΔL, the output spectrum has repeating peaks and dips.
- The spacing between the peaks is approximately `FSR ≈ λ² / (n_g × ΔL)`.

Example: for λ = 1550 nm, n_g ≈ 4.2 (a typical value for a strip waveguide, so check the value your simulation uses) and ΔL = 131.700 µm, the FSR is about 4.34 nm.

Plan three circuits with different ΔL values so that you can see how the peak spacing changes.

## 5. Place the components

1. Open the **EBeam** library in the KLayout library panel.
2. Place the parts you need: grating couplers (input and output), Y-branches for splitting and combining, and a directional coupler if your circuit uses one.
3. Place the same set of parts for each of the three circuits.

## 6. Draw the waveguides

1. Choose the path tool and draw a path on the waveguide layer between the ports of two components.
2. Select the path and convert it with the SiEPIC waveguide command (**SiEPIC → Waveguides → Waveguide from Path**, or the equivalent in your version).
3. Make one arm longer than the other by adding a U-shaped delay loop. Calculate ΔL from the extra length.
4. Keep the bends smooth and the waveguides well apart to avoid coupling and rule violations.

## 7. Add the loopback reference

Place two grating couplers and connect them with one waveguide. Measuring or simulating this structure tells you the loss of the couplers, which you can subtract from the other devices.

## 8. Label every device

Add a text label next to the input grating coupler of each device, using this format:

```
opt_in_TE_1550_device_<YourName><DeviceName>
```

For example `opt_in_TE_1550_device_MohammadullahMZI1`. The label tells the automated measurement setup (and the simulation tool) which devices to find.

## 9. Check your design

1. Run the SiEPIC verification tools (the design rule check and the connectivity or functional layout check) from the **SiEPIC** menu.
2. Run the design-for-test check.
3. Fix every error that appears, then run the checks again until the design passes.

## 10. Simulate

1. Run the circuit simulation from the **SiEPIC** menu, or follow the SiEPIC documentation for your version.
2. Open the plots for each device. Each MZI should show regular peaks and dips.
3. Compare the peak spacing with the FSR formula. A longer ΔL should give peaks that are closer together.
4. Check the loopback result to see the coupler loss.

## 11. Save and share

Save your layout as a GDS file. Take clean screenshots of the layout and of each plot and describe what you built and what you found in your README.

## 12. Common problems

- **No SiEPIC menu:** the packages did not install or KLayout was not restarted.
- **Waveguide will not convert:** the path may not be on the waveguide layer, or it ends away from a component port.
- **Simulation finds no devices:** check that every label follows the `opt_in_TE_1550_device_...` format and sits near the correct grating coupler.
- **Rule violations:** waveguides too close together or bends too tight are the usual causes.

## 13. Learn more

- SiEPIC-Tools: https://github.com/SiEPIC/SiEPIC-Tools
- SiEPIC EBeam PDK: https://github.com/SiEPIC/SiEPIC_EBeam_PDK
- The book *Silicon Photonics Design: From Devices to Systems* by Lukas Chrostowski and Michael Hochberg
- The free online course *Silicon Photonics Design, Fabrication and Data Analysis* from UBC on edX
