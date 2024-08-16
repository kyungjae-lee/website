[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > Unions

# Unions



## Introduction to Unions

* A union in C is similar to a structure except that all of its members start at the same location in memory.

* A union variable can represent the value of only one of its members at a time.

* Use union instead of structure to save memory **when the access to its member elements is mutually exclusive**.

  For example, when sending a packet from a transmitter to a receiver you either use the long address or the short address but not both at the same time. In other words, using the long address and the short address are mutually exclusive.

  ```c
  /* memory efficient - preferred */
  union address
  {
      uint16_t shortAddr;
      uint32_t longAddr;
  };
  ```

  ```c
  /* memory inefficient - not preferred */
  struct address
  {
      uint16_t shortAddr;
      uint32_t longAddr;    
  };
  ```

  

## Exercise

* Following program demonstrates the way unions work.

  ```c
  #include <stdio.h>
  #include <stdint.h>
  
  union address
  {
      uint16_t shortAddr;
      uint32_t longAddr;
  }
  
  int main(int argc, char *argv[])
  {
      union Address addr;
      
      addr.shortAddr = 0xABCD;
      addr.longAddr = 0xEEEECCCC;	/* overwrites the previous assignment */
      
      printf("shortAddr = %#x\n", addr.shortAddr);
      printf("longAddr = %#x\n", addr.longAddr);
      
      return 0;
  }
  ```

  ```plain
  shortAddr = 0XCCCC
  longAddr = 0XEEEECCCC
  ```



## Applications of Unions in Embedded Software

* Benefits:

  * Bit extraction

  * Storing mutually exclusive data thus saving memory

* Following program demonstrates how **union & structure combination** can be used to save memory and make the bit extraction very easy.

  ```c
  #include <stdio.h>
  #include <stdint.h>
  
  union Packet
  {
      uint32_t packetInfo;
      
      struct
      {
          uint32_t crc		:2;
          uint32_t status 	:1;
          uint32_t payload 	:12;	
          uint32_t bat		:3;
          uint32_t sensor		:3;
          uint32_t longAddr	:8;
          uint32_t shortAddr	:2;
          uint32_t addrMode	:1;
      } PacketFields; /* now PacketFields act as a member struct variable name */
  };
  
  int main(int argc, char *argv[])
  {
      union Packet packet;
      uint32_t packetInfo;
  
      printf("Enter 32-bit packet information (in hex): ");   /* 0xFFFFFFFF or FFFFFFFF */
      scanf("%x", &packet.packetInfo);
  
      /* now the following code is no longer necessary
      packet.crc = (uint8_t)(packetInfo & 0x3);
      packet.status = (uint8_t)((packetInfo >> 2) & 0x1);
      packet.payload = (uint16_t)((packetInfo >> 3) & 0xFFF);
      packet.bat = (uint8_t)((packetInfo >> 15) & 0x7);
      packet.sensor = (uint8_t)((packetInfo >> 18) & 0x7);
      packet.longAddr = (uint8_t)((packetInfo >> 21) & 0xFF);
      packet.shortAddr = (uint8_t)((packetInfo >> 29) & 0x3);
      packet.addrMode = (uint8_t)((packetInfo >> 31) & 0x1);
      */
  
      printf("crc       : %#x\n", packet.PacketFields.crc);
      printf("status    : %#x\n", packet.PacketFields.status);
      printf("payload   : %#x\n", packet.PacketFields.payload);
      printf("bat       : %#x\n", packet.PacketFields.bat);
      printf("sensor    : %#x\n", packet.PacketFields.sensor);
      printf("longAddr  : %#x\n", packet.PacketFields.longAddr);
      printf("shortAddr : %#x\n", packet.PacketFields.shortAddr);
      printf("addrMode  : %#x\n", packet.PacketFields.addrMode);
  
      printf("Size of packet (struct) is %lu bytes.\n", sizeof(packet)); /* %I64u */
  
      return 0;
  }
  ```

  ```plain
  Enter 32-bit packet information (in hex): 0xFFFFFFFF
  crc       : 0x3
  status 	  : 0x1
  payload	  : 0xfff
  bat    	  : 0x7
  sensor 	  : 0x7
  longAddr  : 0xff
  shortAddr : 0x3
  addrMode  : 0x1
  Size of packet (struct) is 4 bytes.
  ```

  



## References

Nayak, K. (2022). *Microcontroller Embedded C Programming: Absolute Beginners* [Video file]. Retrieved from  https://www.udemy.com/course/microcontroller-embedded-c-programming/
