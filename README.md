# ECE 528/L: Robotics and Embedded Systems with Lab
## Homework 2

## Section I: Review Questions

Create your own GitHub repository for this homework assignment. Then, provide your answers to the following questions in a README file in your repository. Code statements must be written in C and you must use hexadecimal values for bitmasks. You are encouraged to use external references.

1. **(5 pts)** What is a hardware timer? What kinds of tasks do embedded systems commonly use hardware timers for?

    **A hardware timer is a dedicated circuit inside a microcontroller that counts clock pulses or signals independently of the main CPU. Embedded systems commonly use hardware timers for generating periodic signals or delays such as with PWM.**

2. **(5 pts)** What is a clock signal used for in embedded systems? List the primary clock sources available on the MSP432P401R microcontroller and briefly describe each clock source.

    **Clock signals are used to set the pace of instuction execution and synchronizing operations. The MSP432 has the following clock sources:**
    | Clock Source | Description |
    |---|---|
    | LFXT | Low-frequency external crystal oscillator, 32.768 kHz. |
    | HFXT | High-frequency external crystal oscillator, up to 48 MHz. |
    | DCO | Internal digitally controlled oscillator with selectable frequency up to 48 MHz |
    | VLO | Internal very-low-power, low-frequency oscillator, about 9.4 kHz; lowest power but least accurate. |
    | REFO | Internal low-power reference oscillator, 32.768 kHz (selectable to 128 kHz); used when no LFXT crystal is present. |
    | MODOSC | Internal low-power module oscillator, factory calibrated to 25 MHz; used by peripherals such as the ADC. |
    | SYSOSC | Internal system oscillator, factory calibrated to 5 MHz; used as the fail-safe source and for ADC and system timing. |

    ***This information is found on page 123 of the ti_msp432p401r_datasheet_slas826e.pdf document***
3. **(5 pts)** Consider a 16-bit timer (e.g. Timer_A configured in up mode). If it counts upward, what is the maximum count value before it rolls over? Show your work.

    **The Maximum count is 2^16-1 or 65,535.**

4. **(5 pts)** If a 24-bit timer counts upward from zero at 10 MHz, what is its overflow period? Show your work.

    **Each tick is 1/10MHz or 1/10000000 = 0.0000001 seconds.**

    **A 24 bit timer has 2^24 = 16,777,216 positions counting 0.**
    
    **Our overflow period is then 16,777,216/10000000 = 1.6777216 seconds**

5. **(5 pts)** Describe the SysTick timer on the MSP432P401R microcontroller. Provide two example applications for SysTick in embedded systems. Which clock sources can the SysTick timer use and how can they be selected?

    **The SysTick timer is a 24 bit down-counter that counts down from the value in its reload register to zero. Upon reaching zero, it sets the COUNT flag and can request an interrupt. It then rolls back to the reload value.**

    **Some example applications would be periodically sampling a sensor or generating blocking delays.**

    **The SysTick timer can select the clock input using the CLKSOURCE bit (bit 2 of the CTRL register). Our options for sources are MCLK, our other options are not applicable on our board.**

    ***This information can be found on page 131 of the ti_msp432p4xx_technical_reference_manual_slau356i.pdf document***

6. **(5 pts)** How many registers does the SysTick timer have on the MSP432P401R microcontroller and what is the purpose of each? Name them and provide a short description per register.
    | Register | Name | Description |
    |---|---|---|
    | STCSR | SysTick Control and Status Register | Enables the counter (ENABLE, bit 0), enables the interrupt (TICKINT, bit 1), selects the clock source (CLKSOURCE, bit 2), and holds the COUNT flag (bit 16), which is set when the counter reaches zero. |
    | STRVR | SysTick Reload Value Register | Holds the 24-bit value that is loaded into the counter when it reaches zero. For a period of N ticks, load N − 1. |
    | STCVR | SysTick Current Value Register | Holds the current 24-bit count. Writing any value clears it to zero and clears the COUNT flag. |
    | STCR | SysTick Calibration Value Register | Read-only register. Use the SysTick Calibration Value Register to enable software to scale to any required speed using divide and multiply. |

    ***This information is found on page 130 of the ti_msp432p4xx_technical_reference_manual_slau356i.pdf document***

7. **(5 pts)** Describe the Timer_A peripheral on the MSP432P401R microcontroller. List the main operating modes of Timer_A and briefly explain each.

    **Timer_A is a 16-bit timer/counter. It can support multiple capture/compares, PWM outputs, and interval timing. It can also generate interrupts from the counter using both overflow conditions and from each of the capture/compare registers.**

    **The operating mode is selected by the MC field (Bits 5-4) of the TAxCTL register:**
    | MC | Mode | Description |
    |---|---|---|
    | 00b | Stop | The timer is halted. |
    | 01b | Up | The timer repeatedly counts from 0 to the value in TAxCCR0, then restarts from 0. The period is (TAxCCR0 + 1) ticks. |
    | 10b | Continuous | The timer repeatedly counts from 0 to 0xFFFF, then rolls over to 0. |
    | 11b | Up/Down | The timer repeatedly counts from 0 up to the value in TAxCCR0, then back down to 0. The period is 2 × TAxCCR0 ticks. |

    ***This information is found on page 785 of the ti_msp432p4xx_technical_reference_manual_slau356i.pdf***


8. **(10 pts)** Briefly state the purpose of the following Timer_A registers.
   - TAxCTL
   - TAxCCTLn
   - TAxR
   - TAxCCRn
   - TaxIV
   - TAxEX0

    | Register | Name | Purpose |
    |---|---|---|
    | TAxCTL | Timer_A Control Register | Configures the timer as a whole: selects the clock source (TASSEL), the input divider (ID), and the operating mode (MC), clears the counter (TACLR), and holds the overflow interrupt enable and flag (TAIE, TAIFG). |
    | TAxCCTLn | Capture/Compare Control Register n | Configures channel n: selects capture or compare mode (CAP), the capture edge and input (CM, CCIS), and the output mode (OUTMOD), and holds the channel's interrupt enable and flag (CCIE, CCIFG). |
    | TAxR | Timer_A Counter Register | Holds the current 16-bit count of the timer. |
    | TAxCCRn | Capture/Compare Register n | In compare mode, holds the value that is compared against TAxR (TAxCCR0 sets the period in up and up/down modes). In capture mode, holds the TAxR value that was copied at the capture event. |
    | TAxIV | Timer_A Interrupt Vector Register | Provides a value identifying the highest-priority pending interrupt among TAxCCR1 to TAxCCRn and the timer overflow, so the shared ISR can determine which source caused the interrupt. |
    | TAxEX0 | Timer_A Expansion Register 0 | Holds the TAIDEX field, a second input divider (divide by 1 to 8) that is applied in addition to the ID divider. |

    ***This information is found on page 796 of the ti_msp432p4xx_technical_reference_manual_slau356i.pdf***
9. **(5 pts)** What is PWM? Provide at least three example applications that use PWM in embedded systems.

    **PWM stands for Pulse Width Modulation. Pulse width modulation serves to only activate a
    logical high signal for a certain percentage of a period while weaving in logical low signals into the stream. You can use this to:**

    **1. Dim an LED**

    **2. Control a motor's speed**

    **3. Generate a specific tone from a buzzer**
10. **(5 pts)** How many Timer_A modules are available and how many PWM outputs can each module provide?

    **The MSP432 we use has 4 Timer_A modules (TA0, TA1, TA2, TA3) with 4 PWM outputs each.**

11. **(25 pts)** Describe the steps needed to initialize P2.4 as a PWM output driven by Timer_A0 CCR1 (TA0.1).

    Use the [Timer_A2_PWM.c driver](https://github.com/csun-ece-528-fall26/ECE_528L_Motor_Control/blob/main/ece528L_motor_control/Motor_Control/src/Timer_A2_PWM.c) provided in Lab 1 (Motor Control) as a reference.

    Include C code such that:
    - The P2.4 pin is configured to use its primary module function (Timer_A output)
    - SMCLK is selected as the clock source for Timer_A
    - The timer clock is divided by 2
    - Up/down mode is selected
    - The output mode is configured as Toggle / Reset by setting the appropriate bits of the OUTMOD field in the CCTL[1] register
    - The PWM signal has a period of 20 ms and a duty cycle of 50%

    1. Configure P2.4 for its primary module function and as an output (SEL0 = 1, SEL1 = 0).
    2. Clear the MC field.
    3. Load CCR0 with the period value ()

12. **(20 pts)** Describe the steps needed to configure Timer_A0 to generate a periodic interrupt every 2 ms with an interrupt priority level of 2. Note that Timer_A0 has an interrupt number of 8.

    Use the [Timer_A0_Interrupt.c driver](https://github.com/csun-ece-528-fall26/ECE_528L_Motor_Control/blob/main/ece528L_motor_control/Motor_Control/src/Timer_A0_Interrupt.c) provided in Lab 1 (Motor Control) as a reference.

    Include C code such that:
    - SMCLK is selected as the clock source for Timer_A with a prescaler value of 1 (which means the ID field will be 00b)
    - The TAIDEX field in the TAxEX0 register has a value of 000b (divide by 1)
    - Up mode is selected
