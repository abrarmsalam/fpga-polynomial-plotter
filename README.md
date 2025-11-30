# fpga-polynomial-plotter
Hardware polynomial plotter on DE1-SoC FPGA. Takes PS/2 keyboard input for coefficients (degree 0-5), computes polynomial values using hardware accelerator with Horner's method, and displays smooth curves on VGA using Bresenham's line algorithm. Includes auto-scaling and dual-port RAM for 640x480 resolution.
