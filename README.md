# fpga-polynomial-plotter
Hardware polynomial plotter on DE1-SoC FPGA. Takes PS/2 keyboard input for coefficients (degree 0-5), computes polynomial values using hardware accelerator with Horner's method, and displays smooth curves on VGA using Bresenham's line algorithm. Includes auto-scaling and dual-port RAM for 640x480 resolution.

# FPGA Polynomial Plotter

Hardware implementation of a polynomial function plotter on the DE1-SoC FPGA board (Cyclone V).

## Features

- **Polynomial Degree:** Up to 5th degree (ax⁵ + bx⁴ + cx³ + dx² + ex + f)
- **Input:** PS/2 keyboard for coefficient entry (18 keys: 6 coefficients × 3 keys each)
- **Computation:** Hardware polynomial accelerator using Horner's method
- **Display:** VGA output at 640×480 resolution with Bresenham line drawing
- **Scaling:** Automatic x/y scaling based on polynomial degree
- **Visual:** Blue grid (40px spacing), green axes, red polynomial curve

## System Architecture
```
┌─────────────┐    ┌──────────────┐    ┌─────────────────┐
│   PS/2      │───▶│ Coefficient  │───▶│   Polynomial    │
│  Keyboard   │    │   Builder    │    │  Accelerator    │
└─────────────┘    └──────────────┘    └────────┬────────┘
                                                 │
                           ┌─────────────────────┘
                           │
                    ┌──────▼──────┐    ┌─────────────┐
                    │  Plot FSM   │───▶│  Dual-Port  │
                    │  (640 pts)  │    │     RAM     │
                    └─────────────┘    └──────┬──────┘
                                              │
                                       ┌──────▼──────┐
                                       │  VGA Plot   │
                                       │ Controller  │
                                       └──────┬──────┘
                                              │
                                       ┌──────▼──────┐
                                       │VGA Adapter &│
                                       │   Monitor   │
                                       └─────────────┘
```

## Hardware Requirements

- DE1-SoC FPGA Board (Cyclone V 5CSEMA5F31C6)
- VGA monitor
- PS/2 keyboard
- Quartus Prime 18.0+

## Usage

1. **Enter coefficients** via PS/2 keyboard:
   - Format: `[sign][digit][digit]` for each coefficient (a5 to a0)
   - Sign: `=` for positive, `-` for negative
   - Example: `=01=00=05` → a2=1, a1=0, a0=5 (parabola: y=x²+5)

2. **Press KEY[1]** to compute and display

3. **Press KEY[0]** to reset

## Example Polynomials

| Input | Polynomial | Result |
|-------|-----------|--------|
| `=05` (all others 0) | y = 5 | Horizontal line |
| `=01=10` | y = x + 10 | Diagonal line |
| `=01=00=00` | y = x² | Parabola |
| `=01=00=00=00` | y = x³ | Cubic curve |

## File Structure
```
├── PROJECT_DEMO.v          # Top-level module
├── vga_plot_controller.v   # VGA drawing with Bresenham
├── plot_fsm.v              # Polynomial computation FSM
├── polynomial_accelerator.v # Hardware polynomial evaluator
├── inputparsing.v          # PS/2 keyboard parser
├── coeffbuilder.v          # Scancode to coefficient converter
├── y_table_ram.v           # Dual-port RAM for y-values
├── vga_adapter.v           # VGA timing controller
├── vga_pll.v               # 50MHz → 25MHz clock divider
└── synchroniser.v          # PS/2 clock synchronizer
```

## Technical Details

### Polynomial Accelerator
- Uses **Horner's method** for efficient evaluation: `((((a5·x + a4)·x + a3)·x + a2)·x + a1)·x + a0`
- **6 clock cycles** per evaluation
- 48-bit output precision

### Coordinate Transformation
- Screen x (0-639) → Math x (-320 to +319)
- Math y → Screen y: `y_screen = 240 - y_math`
- Auto-scaling: Higher degree → more aggressive scaling

### VGA Timing
- **Resolution:** 640×480 @ 60Hz
- **Pixel clock:** 25.175 MHz (25 MHz approximation)
- **Color depth:** 3-bit RGB (8 colors)

## License

MIT License - see LICENSE file

## Acknowledgments

- VGA adapter provided by course materials
- Bresenham's line algorithm implementation
- DE1-SoC board pin assignments
