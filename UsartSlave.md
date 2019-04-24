# USART Slave

```c
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>

void USART_init(void){  
  UBRR0 = 103;
  UCSR0B = (1<<RXEN0) | (1<<TXEN0);
  UCSR0C = (1<<UCSZ01) | (1<<UCSZ00);
  
  UBRR1 = 103;
  UCSR1B = (1<<RXEN1) | (1<<TXEN1);
  UCSR1C = (1<<UCSZ11) | (1<<UCSZ10);
}

unsigned char USART_Receive(){
  while ( !(UCSR0A & (1<<RXC0)));
  return UDR0;
}

unsigned char USART_Receive1(){
  while ( !(UCSR1A & (1<<RXC1)));
  return UDR1;
}

void USART_transmit1(unsigned char sData){  
  while(!(UCSR1A & (1<<UDRE1)));
  UDR1 = sData;
}

void USART_transmit(unsigned char sData){
  while(!(UCSR0A & (1<<UDRE0)));
  UDR0 = sData;
}

int search_x(unsigned char regen[]){
	int number = 0;
	for(int i=0;i<5;i++){
			number++;
		}
	return number;
}

unsigned char text[5];
	
int main(void){
  USART_init();
	int number = 0;
	char number_char;
  while (1){
		for(int i = 0; i < 5; i++){
			text[i] = USART_Receive1();
		}	
		for(int i = 0; i < 5; i++){
			if(text[i] == 'x'  || text[i] == 'X'){
				number++;
			}
		}
		number_char = number + '0';
		USART_transmit(number_char);
		//USART_transmit("\n");
	}
}
```

