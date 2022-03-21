# chall_05_pwn_writeup
elf.sym offsets

Checksec - no canary, we can stack smach. 

file - dynamically linked / 64 bit

We run the file and get an address. What could it be? 

  Run the process again with pwntools and debug with radare2. Upon investigation, we find that the address belongs to main. 
  
In radare, there is a main that calls vuln, and an uncalled win function. 

In vuln, we see a 'call rax' after rax is assigned the value from var_8. Interestingly, before that, there is a 'gets' that inputs 0x60 to var_60. 

Turns out we don't need to stack smash. We need to send a payload with 0x60-0x8 bytes junk + p64(winFunctAddress).

How do we find the win function address? Well we know the main address, and there IS a way to find the FIXED offset from main -> win. 

To do this we subtract elf.sym.main from elf.sym.win. 

Our operation to find the main address is as follows:  
  
    **winAddress = mainLeak - (elf.sym.main - elf.sym.win)**
    
The entire script is below:

![image](https://user-images.githubusercontent.com/79220528/159335922-9c6ae08f-e229-4ab3-a909-b158432b21c4.png)
