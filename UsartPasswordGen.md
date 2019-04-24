# Usart Password Generator

```c
#define F_CPU 16000000
#include <util/delay.h>
#include <avr/io.h>
#include <stdlib.h>

void USART0_INIT(void){
	UBRR0L = 0x67; //dezimal 103
	UCSR0B = (1<<RXEN0) | (1<<TXEN0);
	UCSR0C = (1<<UCSZ01) | (1<<UCSZ00);
}

void USART1_INIT(void){
	UBRR1L = 0x67; //dezimal 103
	UCSR1B = (<<RXEN1) | (<<TXEN1);
	UCSR1C = (1<<UCSZ11) | (1<<UCSZ10);
}

void TIMER1_INIT(){
	TCCR1B |= (1<<CS10);
}

void USART_Transmit0(unsigned char sData){
	while(!(UCSR0A & (1<<UDRE0)));
	UDR0 = sData;
}

void USART_Transmit1(unsigned char sData){
	while(!(UCSR1A & (1<<UDRE1)));
	UDR1 = sData;
}

unsigned char USART_Receive(void){
	while (!(UCSR0A & (1<<RXC0)));
	return UDR0;
}

unsigned char USART_Receive(void){
	while (!(UCSR1A & (1<<RXC1)));
	return UDR1;
}

unsigned char[6] Password(){
	char[6] gross;
	for (int i = 0; i < 3; i++){
		gross[i] = rand() % ('Z' + 1 - 'A') + 'A';
	}
	for (int i = 4; i < 6; i++){
		gross[i] = rand() % ('z' + 1 - 'a') + 'a';
	}
	gross[6] = '\0';
	return gross[];
}

int main(void){
	unsigned char[6] password;
	USART0_INIT();
	USART1_INIT();
	TIMER1_INIT();
	int seeded = 0;
	int seed;
	while (1){
		if(USART_Receive() =='g' && seeded != 0){
			seed = TCNT1H;
			seed = seed << 8;
			seed |= TCNT1L;
			srand(seed);
			seeded = 1;
			password = Password();
			for(int i = 0; i < 5; i++){
				USART_Transmit1(password[i]);
			}
		}else if (USART_Receive() =='g'){
			password = Password();
			for(int i = 0; i < 5; i++){
				USART_Transmit1(password[i]);
			}
		}
		_delay_ms(100);
	}
}
```

