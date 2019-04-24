# Fade LED 2

```c

#define F_CPU 16000000UL
#include <avr/io.h>
#include <util/delay.h>
#include <avr/interrupt.h>
uint8_t Helligkeit = 0;
int Modus = 0; //die Variable "Modus" gibt an ob die Helligkeit gerade zu- oder abnimmt

void inittimer(){
	TCCR0A |= (1<<WGM01) | (1<<WGM00) | (1<<COM0A1); //fast PWM + non inverting
	TCCR0B |= (1<<CS01) | (1<<CS00); //64 Prescaler
	TIMSK0 |=(1<<TOIE0); //Interrupt enable
	OCR0A = Helligkeit; //default Wert wird zugewiesen
}

ISR(TIMER0_OVF_vect){
	OCR0A = Helligkeit; //Helligkeit wird aktualisiert
}

int main(void) {
	sei(); //Global Interrupt enable
	DDRB |= (1<<DDB3); //PWM Pin zum Ansteuern der LED als Ausgang definieren
	inittimer(); //Timer initialisieren
	while(1)
	{
		_delay_us(20); //Delay um Blinkgeschwindigkeit zu regulieren
		if(Modus==0){ //LED wird langsam immer heller
			Helligkeit++; //Helligkeit wird erhöht
			if(Helligkeit==0xFF){ // Helligkeit ist am Maximum
				Modus = 1; //Helligkeit wird nun mit jedem Zyklus reduziert
			}
		}
		else if(Modus == 1){ //LED wird langsam immer dunkler
			Helligkeit--; //Helligkeit wird reduziert
			if(Helligkeit==0x00){ //Helligkeit ist am Minimum
				Modus = 0; //Helligkeit wird nun mit jedem Zyklus erhöht
			}
		}
	}
}
```

