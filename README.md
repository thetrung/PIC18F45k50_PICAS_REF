A guide & reference to write PIC Assembly in 2025
=================================================
this was always confused for anyone to handle-on PIC microcontroller, especially ones that are new to this like me. While I don't want to only write code in C, I want asssembly for smaller size & faster performance. So I solved this complex MPASM -> PIC-AS issue (again) & write it down to share for everyone in 2025. 

Why would we waste such simple assembly instruction set like PIC architecture for more complex ones ?

### Setup
This was done on `A morning of 11th January 2025` with :
- Simulator
- PIC18F45k50
- PIC-AS 2.50
- MPLAB X IDE 6.20 (Tab -> Space)
- Ubuntu 24.10 with kernel 6.11

Then verified & refined on `23th March 2025` to a complete Blink on the same 45k50 chip :

- Fixed `PORTA` -> `LATA` : As the same change to C source code, no longer use PORTA but LATA.
- Previous delay wasn't enough so now we have : `DELAY * 256 * 256` when using `INTOSCIO`.
- We can use `MOVF REG,W` to save value of any register into `W-reg`.
- We can't **compare to zero directly** (PICAS v3.00), but rely on 2 methods :
  -  `CPFSEQ REGISTER` : to compare a register value with W-reg.
  -  `BTFSC STATUS, Z` : to test if previous `XORLW 0x00` (with W-reg) result in zero.

### Key Issues
Key problem during debugging was how the linker didn't link the code properly with previous `ORG 0x0000` directive on MPASM.

- **PSECT** : this is now required for linker to link every parts correctly. And `space=[0~3]` argument let us access `program memory, data memory, reserved, eeprom`. Which is quite critical usage on memory.

- **BANKSEL** : directive to select a memory bank in SRAM, because PIC can only access no more than 256 bytes at once, so it divide memory into banks. Ex: `BANKSEL(TRISA)` will translate into `MOVLB 0xF92` to select TRISA bank.

- **Case Sensitive** : from PIC-AS, it start to distinct this matter, so `MAIN` and `main` are different now.

- **#include <xc.inc>** : for stuffs like `TRISA`, `LATA`...

### Debugging
Without `PSECT`, debugger won't see anything in `Program Memory` section because **your code will never be linked**, variables won't be watched :
![img](https://github.com/thetrung/PIC18F45k50_PICAS_REF/blob/master/MPLAB_DEBUG.png)
