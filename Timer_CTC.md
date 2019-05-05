# Timer CTC

### code

```c
#include <avr/io.h>
#include <avr/interrupt.h>

void configTimer0(){	
	//set CTC mode
	TCCR0A |= (1<<WGM01); 	
	// Prescaler N=1024
	TCCR0B |=  (1<<CS00) | (1<<CS02);
	//set compare value = 116
	OCR0A = 0x74;
	//enable local int
	TIMSK0 |= (1<<OCIE0A);
	//delete int Flag 
	TIFR0 |= (1<<OCF0A); 
}

ISR(TIMER0_COMPA_vect){
	PORTC = ~PORTC;
}

int main(void){
	DDRC = 0xFF;
	PORTC = 0xFF;
	configTimer0();
	sei();
	while (1){}
}
```

