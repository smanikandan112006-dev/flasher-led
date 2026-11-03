
# FLASHING OF LEDs WITH LPC1768

## AIM
To interface an LED with the ARM LPC1768 microcontroller and toggle the LED using GPIO programming.

## COMPONENTS REQUIRED

### Hardware
- ARM LPC1768 development board
- LED
- Current-limiting resistor (220 Ω–330 Ω)
- Breadboard and jumper wires

### Software
- Keil µVision 4 IDE
- ARM Compiler
- LPC1768 startup files and CMSIS system files
- `fromelf` utility for generating the binary file

## PROCEDURE

1. Open Keil µVision and select **Project → New µVision Project**.
2. Browse to the project folder, enter the project name, and save it.
3. In the **Select Device for Target** window, select **NXP → LPC1768**.
4. Add the LPC17xx startup file when prompted.
5. Create a new C source file and write the LED blinking program.
6. Save the source file as `main.c`.
7. Add `main.c` to **Source Group 1**.
8. Ensure the project includes the required startup assembly file and `system_LPC17xx.c`.
9. Configure the target for the LPC1768 microcontroller.
10. Set the required IROM1 start address and size according to the board's memory layout and bootloader requirements.
11. Build the project and correct any compilation errors.
12. Configure the post-build command to convert the generated AXF file into a BIN file.
13. Rebuild the project.
14. Verify that the generated binary file exists in the project output folder.
15. Flash the binary file to the board using the appropriate programming tool and observe the LED.

## PROJECT FILE STRUCTURE

```text
FLASHING-OF-LEDS-WITH-LPC1768/
├── README.md
├── main.c
├── startup_LPC17xx.s
└── system_LPC17xx.c
```

The startup file and system file must match the LPC1768 device and the CMSIS/Keil package being used.

## PROGRAM

### main.c

```c
#include <LPC17xx.h>

#define LED_PIN     29
#define LED_MASK    (1U << LED_PIN)

int main(void)
{
    /* Configure P0.22 as GPIO */
    LPC_PINCON->PINSEL1 &= ~(3U << 12);

    /* Configure P0.22 as output */
    LPC_GPIO0->FIODIR |= LED_MASK;

    while (1)
    {
        /* Turn LED ON */
        LPC_GPIO0->FIOSET = LED_MASK;

        /* Software delay */
        for (volatile unsigned int i = 0; i < 500000; i++);

        /* Turn LED OFF */
        LPC_GPIO0->FIOCLR = LED_MASK;

        /* Software delay */
        for (volatile unsigned int i = 0; i < 500000; i++);
    }
}
```

## PIN CONNECTION

| LPC1768 Pin | Connection |
|---|---|
| P0.29 | LED output through a current-limiting resistor |
| GND | LED return path for an active-high connection |

**Note:** The example assumes an active-high LED connected between P0.22 and GND through a resistor. If your development board has an onboard LED or uses active-low wiring, adjust the connection and output logic accordingly.

## KEIL µVISION CONFIGURATION

### 1. Add the Required Files

Add the following files to the project:

Target1:

### Source group1:
 `Startup_lpc17xx.s`, `main.c` , `delay.c` , `system_lpc17xx.c` , `gpio.c` 

### Header:
`Delay.h`, `stdutils.h`, `gpioi.h`

The CMSIS device header `LPC17xx.h` must be available through the project's configured include paths.

### 2. Configure the Memory Address

If your bootloader occupies the memory region from `0x00000000` through `0x00001FFF`, the application may need to start at `0x00002000`.

In **Options for Target → Target**, configure the IROM1 start address to `0x2000` and set the IROM1 size according to the remaining available flash memory.

**Important:** Changing the IROM1 address alone is not sufficient for a bootloader-based application. The startup/vector-table configuration and bootloader handoff must also support the chosen application address. Confirm these details for your specific board.

### 3. Generate the BIN File

Build the project to generate the AXF file.

In **Options for Target → User → After Build/Rebuild**, add a command similar to:

```bat
fromelf --bin ".\Objects\Ledblinker.axf" --output ".\Objects\led.bin"
```

Adjust the AXF path to match the actual output location in your project.

If `fromelf` is not recognized, configure its full executable path or ensure the ARM Compiler installation directory is available to Keil.
## PIN DIAGRAM:

<img width="403" height="496" alt="LPC 1768 PIN diag" src="https://github.com/user-attachments/assets/e2426a0f-18ae-422f-9308-4397157c9932" />

## CIRCUIT DIAGRAM:

<img width="1774" height="887" alt="CIRCUIT DIAGRAM LED BLINKER" src="https://github.com/user-attachments/assets/444f5ec3-7a51-4814-b14d-5fa37d0488f1" />


## OUTPUT

<img width="600" height="800" alt="LED BLINKER 1" src="https://github.com/user-attachments/assets/f9899b7c-8e1a-40c8-b3d8-96ea7578ba84" />


## Result: 
The LED was interfaced with the LPC1768 microcontroller and toggled using GPIO programming.
