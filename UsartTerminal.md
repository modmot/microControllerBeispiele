#  Usart transmit in Terminal (kommt nicht)

```c
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>

void USART_init(void){
	UBRR0 = 103; //set Baudrate
  // enables USART Receiver, enables USART Transmitter
	UCSR0B = (1<<RXEN0) | (1<<TXEN0);
  // sets Character Size to 8bit
	UCSR0C = (1<<UCSZ01) | (1<<UCSZ00);
}

void USART_transmit(unsigned char data){
  // If UDREn is set, the buffer is empty and therefore ready to be written.
	while(!(UCSR0A & (1<<UDRE0)));
	UDR0 = data;
}

unsigned char USART_receive(){
  // Flag is set when unread data in receive buffer 
  // Flag is cleared when the receive buffer is empty
	while(!(UCSR0A & (1<<RXC0)));
	return UDR0;	
}

unsigned char text[] = {"Hello\r\n"};
	
int main(void){
	USART_init();
  while(1){
		if(USART_receive() == 'r'){
			for(int i=0;i<7;i++){
				USART_transmit(text[i]);
			}
		}
		_delay_ms(100);
	}
}

```

