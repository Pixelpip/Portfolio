---
title: "CHIP-8 emulator in C"
date: 2026-05-20 17:00:00
tags:
  - Chip-8
  - raylib
  - C
  - pointers
categories:
  - Dev
---

So recently I completed my long awaited/procrastinated project of building a CHIP-8 emulator/interpreter in C language. Learnt a lot about how to actually use pointers , how bitwise manipulation works , some raylib and other fun stuff.


Check out the project here [Github repo](https://github.com/Pixelpip/CHIP-8)

## Why Chip-8?

Because its one of the easiest to implement emulator compared to anything like NES or GBA with very few opcodes to handle and also is kind of an introduction to systems programming .

## The setup

I'll go over the implementation in the way I went about it so if anyone wants to implemenmt the same it would be easier if you follow the same sequence .

### The hardware 

The hardware can be defined entirely within a struct with just a few unsigned integer of varying width , check out the below code and its comment. All of which are placed in a header file called ```Chip8.h```

```C

typedef struct {
    uint8_t ram[MEM_SIZE]; //8bit=1byte , 4*1024 bytes=4KB
    uint8_t V[NUM_REGISTER]; // V0-VF registers
    uint16_t I; // memory address storage register 
    uint16_t PC;// program counter register
    uint16_t SP; // stack pointer
    uint16_t stack[STACK_SIZE]; // 16 level stack totalling 32 byte
    uint8_t keypad[NUM_KEYS]; // hex keypad 0-F
    uint8_t display[SW*SH]; // 32 rows(height) 64 columns(width) 
    uint8_t ST,DT; //sound registers ST=Sound timer DT=Delay Timer 
    uint16_t opcode; //current opcode 
    uint8_t draw_flag; //should draw or not


}Chip8;


``` 

Here are the few Constants I used in the struct 

```C

#define MEM_SIZE 4096
#define NUM_REGISTER 16
#define STACK_SIZE 16
#define NUM_KEYS 16
#define SW 64
#define SH 32
#define FONT_SIZE 80
#define FONT_ADDR 0x50 
#define PGM_START 0x200


```

You can check out the CHIP-8 reference manual linked below to understand why each component is sized as above.As for the display I decided to use a 1D array with row major ordering over a 2D array because the pointer arithmetic is easier for a 1D array.


### The core functions

There are 3 important functions  defined within the ```Chip8.h``` file which handle basically 90% of the project . I'll go over it now one by one.

* ```void init_chip8(Chip8 *chip8); ```

This function is used to just set the entire RAM array to 0 so we can start writing data to it without any garbage values interrupting any OPcode implementations later and also load the Fonts for each character into the ram starting at the address 0x50.

* void load_rom(Chip8 *c,const char *filename); 

This function is used to load the actual game rom using the FILE type and check if the rom actually contains any data using the ```byte_read`` variable and throw errors accordingly.

* ```void emulate_cycle(Chip8 *c);```

Now this function is the heart of the program because it handles how the opcodes fetched from the roms are worked out so that raylib can draw appropriately on screen.

The hardest part of implementing this function was  how do I even start writing this function and how do I link it with other parts of the program if that makes sense since it was my first project actually using C and I was confused coming from mostly from javascript  and python like languages where packages setup a lot of the project beforehand . I checked out a few video series on implementing CHIP8 emulators on youtube but ultimately went ahead with watching the 6 part series by Queso Fuego but I wanted to build something that would be more simplified, not have the entire project in a single source file and used raylib instead of SDL2 because raylib was the first graphics library I ever used and I wanted to try using it in an actual project.

Then I came across a github repo by Raul Alazovi which implemented the whole emulator in a very beautiful manner which I ended up implementing on my project . All the references will be linked down below .

So to get things started I'll go over the function's implementation mainly about what I did before the OPcodes where processed because processing the OPcode is fairly easy by reading just the reference manual.

- ```uint16_t opcode=(chip8->ram[chip8->PC]<<8)|(chip8->ram[chip8->PC+1]); ```

Since the RAM in CHIP8 is of 8bits but each OPcode is of length 16bits each so we need to merge the two parts into a single 16bit and store it into a 16bit variable . The first half or 8bits of the opcode is fetched from where the Program Counter currently points to and then shifting those bits to the right by 8 places then OR it with the next 8bits within the RAM resulting in a 16bit full opcode.

Then we need to make the PC point to two places above in the RAM so that the next OPcode can be fetched.

- ```NNN``` this variable is used to specify the 12 bit range of the address to jump to 
- ```KK``` it is a 8bit long number which is to be stored within the general purpose registers.
- ```n``` this is used to find how many rows tall each sprite is 
- ```X & Y``` these are pointers which tell us which general purpose register(V[]) to use

Now onto the best part , processing the opcodes . After reffering to the github repo I mentioned earlier , there all the OPcodes were divided based on their Hexadecimal digits and based on that they were processed using switch cases .

like for example each opcode is of 16bits which means each hex digit is 4bits so each opcode would be ```0xABCD``` .

We can create switch statements such that we first check the Most significant 4bits which is 'A' so if a opcode starts with 0x1... we first used a switch statement such as ```switch(opcode&0xF000)``` which ends up fetching the first 4bits and if it corresponds to 0x1... we set up switch cases for that and if the opcode has further bits that need to be checked we can just create a nested swtich for that particular case using the same method and process each opcode in this manner .You can check the implementation of opcode 0x8000 to understand what I mean.

Most of the opcodes were fairly straighforward to implement by just reading the reference manual but ```0xD000``` was the hardest to implement in this project.

This is the Opcode that manages the pixel drawing on screen so it inlvoves some degree of math. 

```C
uint8_t x_pos = chip8->V[X] % SW;
uint8_t y_pos = chip8->V[Y] % SH;
```

The main working of this opcode is that it sets the coordinates on where each pixel should be white or black on screen using (V[X],V[Y]) coordinates , since the V[] registers are 8 bits unsigned it can store values greater than 64 or 32 which is the screen size of the Chip8 so to wrap around the coordinates we use the modulus operator with Screen width and heigh then wrap around so that coordinates never go off screen using the code snippet above. 


Then we assume there is no collison i.e overlapping of pixels at a specific coordinate on screen ```chip8->V[0xF] = 0;``` like when the ball hits player in pong game two objects may reach the same pixel at the same time which is what we mean by a collison .

Then we fetch each row from the RAM and store the byte into the ```sprite_byte``` variable 

After which we need to & it with the same variable with the number 8 which is right shifted in iteration of column in each row , creating a sort of sliding mask and if after the bitwise operation we find that the bit is 1 then we draw on that particular coordinate using row major ordering of the display and then set the draw flag to 1 indicating we have been drawing on this position .

### main.c file

In this file i initalized raylib and also take the inputs from user for which ROM to run .
All the raylib functions are placed within a while loop which runs as long as the window isnt closed .
I used the raylib Key definitions which correspond to the qwerty keyboards we use today and mapped them to the appropriate keys which are used by CHIP8 devices of back then.

Most modern computers run on 60Hz and the CPU speed of CHIP8 was approx 600MHz so we can process about 10 opcodes per frame so I used a 10 iteration for loop and passsed the struct of the device it to the emulate_cycle function via pass by reference.

The DT/delay timer is used to time the events when a ROM is running like for example a running pong game might need the DT to determine when again the ball has to move on screen . I havent yet implemented Sound timer yet so its just there without any usage as of now but it would work similarly i.e to just provide a beep when a event happens.

Now onto the part where raylib actually draws on screen . Its just a simple for loop where each element in the matrix is checked (used row major calculation here) and if it is not 0 which means its 1 then we actually draw White color on screen .

## Conclusion

So that was my writing on how I implemented a emulator , it was easier than I expected it to be though there are few things I still do not know how to fix but anyone reading this does drop a comment below or a PR maybe on the repo.

## References

* [Youtube playlist ](https://www.youtube.com/playlist?list=PLT7NbkyNWaqbyBMzdySdqjnfUFxt8rnU_)
* [Github repo](https://github.com/raula09/CHIP-8_Emulator)
* [Chip8 Reference Manual](http://devernay.free.fr/hacks/chip8/C8TECH10.HTM)










