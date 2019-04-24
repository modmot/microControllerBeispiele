# Timer_01

```c
#include <avr/io.h>
#define F_CPU 16000000
#include <avr/interrupt.h>
#include <util/delay.h>

void initTIMER0()
{
	TCCR0A &= ~(1<<WGM00);	
	TCCR0A &= ~(1<<WGM01);
	TCCR0B &= ~(1<<WGM02);
	TCCR0B &= ~(1<<CS00);
	TCCR0B &= ~(1<<CS01);
	TCCR0B |= (1<<CS02);
	TIMSK0 |= (1<<TOIE0);
	TIFR0 |= (1<<TOV0);
}

ISR(TIMER0_OVF_vect){
	PORTA = ~(PORTA); //toggle PA0
}

int main(void){	
	DDRA=0xFF;
	PORTA=0x01;
	sei();
	initTIMER0();
  while (1){}
}
```

