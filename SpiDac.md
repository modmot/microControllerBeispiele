# SPI mit DAC

```c
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>
uint16_t value=0;

void initSPI(){
	DDRB |= (1<<4) |(1<<5)|(1<<7);
	SPCR |=(1<<MSTR) | (1<<SPE);
	SPSR |=(1<<SPI2X);
}

void spi_transmit(uint8_t word){
	SPDR = word;
	while(!(SPSR&(1<<SPIF)));
}

int main(void){
	initSPI();
	while (1){
		value+=4;
		//SS low
		PORTB &= ~(1<<4);
		//send HB
		spi_transmit(value >> 8);
		//set LB
		spi_transmit(value & 0x00FF);
		//SS high
		PORTB |= (1<<4);
		//if max set to min	
		if(value==0x9FFC){
			value =0x9000;
		}
	}
}
```

```c
#include <avr/io.h>

void initSPI(){
	DDRB = 0xFF;
	SPCR |= (1<<SPE) | (1<<MSTR) | (1<<SPR1);
}

void SPI_send(uint8_t word){
	SPDR = word;
	while(!(SPSR&(1<<SPIF)));
}

int main(void){
	uint8_t hb = 0x93;
	uint8_t lb = 0x34;
	initSPI();
  while(1){
		PORTB |= (1 << PB4);
		SPI_send(hb);
		SPI_send(lb);	
		PORTB &= ~(1 << PB4);
    }
}
```

