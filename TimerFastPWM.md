# Fast PWM

```c
 #include <avr/io.h>
 #define F_CPU 16000000
 #include <avr/interrupt.h>
 #include <util/delay.h>

 void initTIMER0(){
	 OCR0A = 0x1A;
	 TCCR0A |= (1<<WGM01) | (1<<WGM00) | (1<<COM0A1); //fast PWM +  non inverting
	 TCCR0B |= (1<<CS02);		//Prescaler auf 256 gesetzt
	 TIMSK0 |= (1<<TOIE0);		//Compare Match A enablen
	 TIFR0  |= (1<<TOV0);		//Overflow Flag
 }

 ISR(TIMER0_OVF_vect){
	TCCR0A ^= (1<<COM0A0);  // OC0A Toggeln
 }

 int main(void){
	 DDRB=0xFF;			//alle auf Ausgang
	 PORTB=0x00;		//erstes Bit auf High
	 sei();					//Interrupt GLobal aktivieren
	 initTIMER0();	//Interrupt aufrufen
	 while (1){}
 }
```

