# Usart Password Generator

```c
#define F_CPU 16000000
#include <util/delay.h>
#include <avr/io.h>
#include <stdlib.h>

void TIMER1_INIT(){
	TCCR1B |= (1<<CS10);
}

void USART0_INIT(void){  
  UBRR0 = 103;
  // enables USART Receiver, enables USART Transmitter
	UCSR0B = (1<<RXEN0) | (1<<TXEN0);
  // sets Character Size to 8bit
	UCSR0C = (1<<UCSZ01) | (1<<UCSZ00);
}

void USART1_INIT(void){
  UBRR1 = 103;
  // enables USART Receiver, enables USART Transmitter
  UCSR1B = (1<<RXEN1) | (1<<TXEN1);
  // sets Character Size to 8bit
  UCSR1C = (1<<UCSZ11) | (1<<UCSZ10);
}

void USART_Transmit0(unsigned char sData){
  // If UDREn is set, the buffer is empty and therefore ready to be written.
  while(!(UCSR0A & (1<<UDRE0)));
  UDR0 = sData;
}

void USART_Transmit1(unsigned char sData){  
  // If UDREn is set, the buffer is empty and therefore ready to be written.
  while(!(UCSR1A & (1<<UDRE1)));
  UDR1 = sData;
}

unsigned char USART_Receive0(void){
  // Flag is set when unread data in receive buffer 
  // Flag is cleared when the receive buffer is empty
	while (!(UCSR0A & (1<<RXC0)));
	return UDR0;
}

unsigned char USART_Receive1(void){
  // Flag is set when unread data in receive buffer 
  // Flag is cleared when the receive buffer is empty
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
		} else if (USART_Receive() =='g'){
			password = Password();
			for(int i = 0; i < 5; i++){
				USART_Transmit1(password[i]);
			}
		}
		_delay_ms(100);
	}
}
```

