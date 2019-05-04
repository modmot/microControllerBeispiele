# I²C - Temperatursensor auslesen

```c
#define 	F_CPU   16000000  // 16MHz
#define		WR_DEV	0x90  	  // user defined write device address
#define 	RD_DEV	0x91  	  // user defined read  device address
#define   BITRATE 42  	    // overall division factor 100
#include 	<avr/io.h>
#include  <util/delay.h>

void TWinit(unsigned char bitrate) {	// set SCL 160kHz
	TWBR |= bitrate;
	TWSR = 0x00; // set division factor 4^0=1
}

void TWtransmit(unsigned char address, unsigned char data_tx) {
	TWCR |= (1<<TWINT) | (1<<TWSTA) | (1<<TWEN); 	// set start condition
	while (!(TWCR & (1<<TWINT)));									// wait until TWINT is set 

	TWDR = address;																// prepare address in advance
	TWCR |= (1<<TWINT) | (1<<TWEN);								// send address
	while (!(TWCR & (1<<TWINT)));									// wait until TWINT is set 

	TWDR = data_tx;
	TWCR |= (1<<TWINT) | (1<<TWEN);								// send data
	while (!(TWCR & (1<<TWINT)));									// wait until TWINT is set

	TWCR = (1<<TWINT) | (1<<TWSTO) | (1<<TWEN); 	// set stop condition
}

unsigned char TWreceive(unsigned char address) {
	unsigned char data_rx;												// received data

	TWCR |= (1<<TWINT) | (1<<TWSTA) | (1<<TWEN); 	// set start condition
	while (!(TWCR & (1<<TWINT)));									// wait until TWINT is set 

	TWDR = address;																// prepare address in advance
	TWCR |= (1<<TWINT) | (1<<TWEN);								// send address
	while (!(TWCR & (1<<TWINT)));									// wait until TWINT is set 
  
	// Acknowledge status can be directly added to the data read transfer
	TWCR |= (1<<TWINT) | (1<<TWEN) | (1<<TWEA);		// start receiving 1st data + ACK
	while (!(TWCR & (1<<TWINT)));	    						// wait until TWINT is set
	data_rx = TWDR;
	
	//TWCR = (1<<TWINT) | (1<<TWEN);								// start receiving 2nd data + NACK
	//while (!(TWCR & (1<<TWINT)));	   							// wait until TWINT is set
	
	TWCR |= (1<<TWINT) | (1<<TWSTO) | (1<<TWEN); 	// set stop condition
	return data_rx;
} 

int main(void) {
	unsigned char inval, outval;									// input and output value
	DDRA = 0xFF; 																	// Testdata output
	TWinit(BITRATE);
	// write initialisation to LM75
	TWtransmit(WR_DEV, 0x05);
	while(1) {
		inval = TWreceive(RD_DEV);
		PORTA = inval;															// write temperature value to output	
		_delay_us(50);	
	}
}
```



### TWReceive aus dem Test

```c
unsigned char TWreceiveMitKomma(unsigned char address){
	unsigned char data_hb, data_lb;
	
	TWCR |= (1<<TWEN) | (1<<TWSTA) | (1<<TWINT);
	while(!(TWCR &(1<<TWINT)));
	
	TWDR = address;
	TWCR |= (1<<TWINT) | (1<<TWEN);
	while(!(TWCR &(1<<TWINT)));
	
	TWCR |= (1<<TWINT) | (1<<TWEN) | (1<<TWEA);
	while(!(TWCR &(1<<TWINT)));
  data_hb = TWDR
    
  TWCR |= (1<<TWINT) | (1<<TWEN);
	while(!(TWCR &(1<<TWINT)));
  data_lb = TWDR
    
  unsigned int data_rx = (data_hb << 8) + data_lb;
	
	TWCR = (1<<TWINT) | (1<<TWEN) | (1<<TWSTO);
	return data_rx;
}
```



### Code 2

```c
#define F_CPU 16000000
#define WR_DEV 0x90
#define RD_DEF 0x91
#define BITRATE 42

#include <avr/io.h>
#include <util/delay.h>

void TWinit(unsigned char bitrate){
	TWBR |= bitrate;
	TWSR |= 0x00;
}

void Transmit(unsigned char address, unsigned char data_tx){
	TWCR = (1<<TWINT) | (1<<TWSTA) | (1<<TWEN);
	while(!(TWCR &(1<<TWINT)));
	
	TWDR = address;
	TWCR = (1<<TWINT) | (1<<TWEN);
	while(!(TWCR &(1<<TWINT)));
	
	TWDR = data_tx;
	TWCR = (1<<TWINT) | (1<<TWEN);
	TWCR = (1<<TWINT) | (1<<TWSTO) | (1<<TWEN);
}

unsigned char TWreceive(unsigned char address){
	unsigned char data_rx;
	
	TWCR |= (1<<TWEN) | (1<<TWSTA) | (1<<TWINT);
	while(!(TWCR &(1<<TWINT)));
	
	TWDR = address;
	TWCR = (1<<TWINT) | (1<<TWEN);
	while(!(TWCR &(1<<TWINT)));
	data_rx = TWDR;
	
	TWCR |= (1<<TWINT) | (1<<TWEA) | (1<<TWEN);
	while(!(TWCR &(1<<TWINT)));
	
	TWCR = (1<<TWINT) | (1<<TWSTO) | (1<<TWEN);
	return data_rx;
}
int main(void){
  TWinit();
  while (1) {
    //Aufruf
  }
}
```

