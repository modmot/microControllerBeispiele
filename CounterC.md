# Counter

```c
#define F_CPU 16000000
#include <avr/io.h>
#include <avr/delay.h>

int main(void){
	int count = 0;
	DDRA=0xFF;
	PORTA=0x00;
	while(1) {
		count=0;
		while (count<127) 
			PORTA=count;
			_delay_us(16);
			count++;
		}
	}
}
```

