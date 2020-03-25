## Owner:

> CaptainFreak

#### Solved?

 - Yes

#### Solution:

Solved with compile time errors = `#include "/flag"` = easy

https://blog.shoebpatel.com/2020/03/23/FireShell-CTF-2020-Write-up/

#### Other Solutions:

Could not find effective ways to read and retain files at compile time in binary.

You can do it with \_\_asm\_\_ 
https://www.geeksforgeeks.org/c-asm-declaration/

```
__asm__(".globl file\nfile: .incbin \"/tmp/flag\"");

int main(){
	//nothing
}
```

\_\_asm\_\_  takes assembly code to be executed at compile time and its results retained to be used later. very relevant for our use case.

above code retains the flag file.
and after doing:

```
hexdump -c a.out
```

we get the flag in there.
