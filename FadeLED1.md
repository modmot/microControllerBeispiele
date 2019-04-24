# Fade LED 1

## Fast PWM

```c
 #include <avr/io.h>
 #define F_CPU 16000000
 #include <avr/interrupt.h>
 #include <util/delay.h>

const uint16_t pwmtable_8D[32] = {
	 0, 1, 2, 2, 2, 3, 3, 4, 5, 6, 7, 8, 10, 11, 13, 16, 19, 23,
	 27, 32, 38, 45, 54, 64, 76, 91, 108, 128, 152, 181, 215, 255
};
void initTIMER0(){
	 //OCR0A = 0x00;
	 TCCR0A |= (1<<WGM01) | (1<<WGM00) | (1<<COM0A1); //fast PWM + non inverting
	 TCCR0B |= (1<<CS02);			//Prescaler auf 256 gesetzt
	 TIMSK0 |= (1<<TOIE0);		//Overflow Interrupt enablen
	 TIFR0  |= (1<<TOV0);			//Overflow Flag
}
uint8_t index,cnt;
ISR(TIMER0_OVF_vect){
	cnt++;
}
int main(void) {
	DDRB=0xFF;			//alle auf Ausgang
	PORTB=0x00;			//erstes Bit auf High
	sei();					//Interrupt GLobal aktivieren
	initTIMER0();		//Interrupt aufrufen
	while (1) { 
		if(cnt==5){
			index++;
		 	OCR0A=pwmtable_8D[index];
		 	cnt=0;
	 	}
	}
}
```

$$
Tt= \frac{N}{fclkio}*(OCR0A+1)
$$

