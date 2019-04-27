# I²C und SPI

```c
#define F_CPU 16000000
#define WR_DEV 0x90 // write device adress
#define RD_DEV 0x91 // read device adress
#define BITRATE 42	// set SCL 160kHz

#include <avr/io.h>
#include <util/delay.h>

void TW_init(unsigned char bitrate){
	TWBR |= bitrate;
	TWSR = 0x00;
}

void initSPI(){
  // set CS, MOSI, SCK to output
	DDRB |= (1<<4) | (1<<5) | (1<<7);
  //Master enable, SPI enable, set prescaler to 64
	SPCR |=(1<<MSTR) | (1<<SPE) | (1<<SPR1);
}

void SPI_Send(uint8_t wert){
	SPDR = wert;
	while(!(SPSR & (1<<SPIF)));	// waits till Flag is set
}

uint8_t TW_receive(unsigned char adress){
	uint8_t data_rx;
	
  //Interrupt enable, start condition, TWI enable
	TWCR = (1<<TWINT) | (1<<TWSTA) | (1<<TWEN);
	while(!(TWCR &(1<<TWINT)));
	
	TWDR = adress;
	TWCR = (1<<TWINT) | (1<<TWEN);
	while(!(TWCR&(1<<TWINT)));
	
	TWCR = (1<<TWINT) | (1<<TWEN);
	while(!(TWCR&(1<<TWINT)));
	data_rx = TWDR;
	
  // send stop condition
	TWCR = (1<<TWINT) | (1<<TWSTO) | (1<<TWEN);
	return data_rx;
}

int main(void) {
	DDRB=0xFF;
	TW_init(BITRATE);
	initSPI();
	uint8_t temp=0;
	while (1) {
		PORTB &= ~(1 << PORTB0);
		temp=TW_receive(RD_DEV);
		SPI_Send(temp);
		SPI_Send(0);
			
		PORTB |=  (1 << PORTB0);
		_delay_ms(1);
		PORTB &= ~(1 << PORTB0);
		_delay_ms(1);
	}
}
```

