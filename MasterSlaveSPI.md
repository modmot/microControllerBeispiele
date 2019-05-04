# Master Slave Austausch

## Master Code

```c
#define F_CPU 16000000
#include <avr/io.h>
#include <util/delay.h>

void initSPI(){
  //SCK, MOSI,CS, SCPH auf 0xFF bevor SPI Settings
	DDRB |= (1<<4) | (1<<5) | (1<<7);
	SPCR |= (1<<SPE) | (1<<MSTR); //4MHZ SPI takt
}

void SPI_send(unsigned int x){
  //sendet alle Werte vom MOSIraus zum Baustein
	SPDR = x;
  //Wenn der serielle Transfer fertig ist wird die SPI Flag gesetzt/
	while(!(SPSR&(1<<SPIF)));
}

int main(void){
	initSPI();
  while (1){
  	PORTB &= ~(1<<4);	//stcp=0
    SPI_send(0xAA);
    PORTB |= (1<<4);		//stcp=1
    //Ausgabe ist das erste Bitmuster
    _delay_us(1);
      
    PORTB &= ~(1<<4); //stcp=0
    SPI_send(0x55);
    //Ausgabe des zweiten Bitmusters
    PORTB |= (1<<4);		//stcp=1
    _delay_us(1);
  }
}
```



## Slave Code

```c
#include <avr/io.h>
#define F_CPU 16000000
#include <util/delay.h>

uint8_t data[5] = { 0x02 , 0x1A , 0xA1 , 0x46 , 0x13 };

void intiSPI_Slave(){
	DDRB |= (1<<4)|(1<<5)|(1<<7);				//SCK, MOSI, CS, SCPH auf 0xFF bevor SPI Settings
	SPCR |= (1<<SPE)|(1<<MSTR)|(1<<SPR1);				//4MHz SPI takt ||| Slave setzen
}

void SPI_send_Master(uint8_t x){
	PORTB &= ~(1<<PB4);
  SPDR = x;						//schickt alle Werte vom MOSI raus zum Baustein
	while (!(SPSR & (1<<SPIF)));	//When serial transfer complete, the SPIF Flag is set
	PORTB |= (1<<PB4);
  PORTD = SPDR;
}

int main(void) {
	DDRD = 0xFF;
	DDRA = 0xFF;
	intiSPI_Slave();
	//SPDR = 0xC3;
	while (1)
	{
		for(int i =0; i<5;i++){
			SPI_send_Master(data[i]);
			_delay_ms(250);
		}
	}
}
```

