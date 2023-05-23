## Guess The Code
- NAME: guess-the-code
- CATEGORY: None
- POINTS: 200
- DOWNLOAD LINK: http://chal.ctf-league.osusec.org/guess-the-code
- ACCESS: nc chal.ctf-league.osusec.org 1382
- DESCRIPTION: Guess the code and get the flag!

This challenge had a random number generator, which used the current time as its seed.
The program then prompted the user for a code, which was a random number generated and
then modulo-operator-ed by 10000000. 

If the user provides the correct code, the program then presents a menu:
> If you want access to my data, you'll have to send me the right code
> Send the code here: Congrats! You guessed the code, now you can have access to my data
> What would you like to see?
> 
>  1. My video collection
> 
> 2. A great cheat sheet
> 
> 3. The flag
> 
> 4. Exit

Typing 3 displays the flag. 

There were several ways to solve this challenge. The easiest way hinged on the fact that
if the user did NOT provide the code correctly, the program wrote out the correct code before terminating. 
Since the program is time-based, we can use pwntools to launch two instances of the 
program at the same time, as they will have the same seed and therefore the same pseudo
random number. We can then set one up to fail, and grab the correct code. We then provide 
that code to the second instance and enter interactive mode.

---
### Solve Script
First we import our modules:
```
from pwn import *
import time
import random
```

Set up the processes, p and r, (both local -- to test -- and remote):
```
#p = process('./guess-the-code')
#r = process('./guess-the-code')
p = remote('chal.ctf-league.osusec.org', 1382)
r = remote('chal.ctf-league.osusec.org', 1382)
```

Failing the first process and stealing its code:
```
p.recv()
p.sendline(b'888')
p.recv()

code = p.recv()
code = code.decode()
code = code[-8::]
print(code)
```

Sending the code to the second process:
```
r.recv()
r.sendline(code.encode())
r.interactive()
```

Now we're at the menu! Press `3`, and the program prints the flag. 
`osu{timIN9_4Tt4CK5_4r3_JUicY}`
