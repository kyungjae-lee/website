[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">MCU Peripheral Drivers</a> > Introduction to I2C

# Introduction to I2C



## Inter-Integrated Circuit (I2C) Protocol

* Pronounced as "I squared C" or "I two C"
* I2C is a protocol to achieve serial data communication between integrated circuits (ICs) which are close to each other. (Considered more serious protocol than SPI because companies have come to design a specification.)
* I2C protocol details such as how data should be sent/received, how hand shaking should happen between sender and receiver, error handling, etc. are more complex than those of SPI's. In other words, SPI is a simpler protocol compared to I2C.)



## I2C Terminology

* Definitions of I2C bus terminology

  | Term            | Description                                                  |
  | --------------- | ------------------------------------------------------------ |
  | Transmitter     | Device sending data (to bus)                                 |
  | Receiver        | Device receiving data (from bus)                             |
  | Master          | Device that initiates data transfer, generates clock signals and terminates data transfer |
  | Slave           | Device addressed by master                                   |
  | Multi-master    | More than one master can attempt to control the bus at the same time without corrupting the message |
  | Arbitration     | Procedure to ensure that if more than one master tries to control the bus simultaneously, only one is allowed to do so and the winning message is not corrupted |
  | Synchronization | Procedure to synchronize the clock signals of two or more devices |

  > I2C communication is always initiated by the master.



## Difference Between SPI and I2C

* I2C is based on a dedicated global specification which can be downloaded from https://www.nxp.com/docs/en/user-guide/UM10204.pdf.

  For SPI, however, there's no dedicated global specification. Only some vendors such as TI and Motorola have their own proprietary specifications.

* I2C supports multi-master mode, whereas SPI provides no official guidelines to achieve this (it depends on MCU designers or vendors.)

  e.g., STM SPI peripherals can be used in multi-master mode, but software is responsible for the arbitration. (In the case of I2C, arbitration is taken care of by the hardware automatically.)

  $\to$ One of the most important features of the I2C peripheral!

* I2C hardware automatically ACKs upon reception of every byte, whereas SPI does not support automatic ACKing. (Must be implemented in the software)
* I2C needs just 2 pins to connect all the masters and slaves, whereas SPI may need 4 or more pins depending on the number of slaves involved in communication.



<img src="img/i2c-2-pins.png" alt="i2c-2-pins" width="800">



* I2C master talks to slaves based on their addresses, whereas in SPI a dedicated pin (SS) is used to select the slave to talk to. (Each device has their own address.)

* I2C is half-duplex, whereas SPI is full-duplex.

* I2C's max speed is 4 MHz in ultra speed plus mode (which is supported by some higher-end MCUs). For some STM MCUs, the max speed is even slower (e.g., 400 KHz). However, SPI's max speed is its F~pclk~/2, which means that if the peripheral clock is 20 MHz, then SPI max speed can be 10 MHz.

  I2C is slower than SPI in general!

* In I2C, slave can make master wait by holding the clock down if it's busy thanks to the clock stretching feature (supported by most of the MCUs) of I2C. However, in SPI, slave has no control over the clock. Programmers must come up with their own tricks and solutions to overcome this situation.

* I2C's data rate (i.e., number of bits transferred from sender to receiver in 1 second) is much less than that of the SPI's. For example, when the STM32F4xx MCU supports the peripheral clock of 40 MHz, SPI can support up to 20 Mbps, whereas I2C can support only 400 Kbps.

  Due to this advantage of SPI over I2C, it is impossible to simply replace all SPI applications with that of I2Cs.



## SDA & SCL Signals

* Both the SDA and SCL are bidirectional lines connected to a positive supply voltage via pull-up resistors. When the bus is free, both lines are pulled to HIGH.
* The output stages of devices connected to the bus must have an open-drain or open-collector configuration.
* The bus capacitance limits the number of interfaces connected to the bus.
* Both the SDA and SCL pins must be in open-drain configuration and must use pull-up resistors (either internal or external).



<img src="img/i2c-pin-configuration.png" alt="i2c-pin-configuration" width="800">



* Troubleshooting tip: When the bus is idle, both the SDA and SCL are pulled to +V~DD~.

  Whenever you face problems in I2C, probe the SDA and SCL line after I2C initialization. (The voltage between GND-SDA(or SCL) should read 3.3V or 1.8V depending on the I/O voltage levels of the board.)



## I2C Modes

| Mode            | Data Rate      | Notes                                                    |
| --------------- | -------------- | -------------------------------------------------------- |
| Standard mode   | Up to 100 Kbps | Supported by STM32F4xx                                   |
| Fast mode       | Up to 400 Kbps | Supported by STM32F4xx                                   |
| Fast mode +     | Up to 1Mbps    | Supported by some STM32F4xx (check the reference manual) |
| High speed mode | Up to 3.4 Mbps | Not supported by STM32F4xx                               |

> bps: bits per second

### Standard Mode

* In standard mode communication, data transfer rate can reach up to maximum of 100 Kbps.
* Standard mode was the very first mode introduced when the first I2C specification was released.
* Standard-mode devices, however, are not forward compatible. (They cannot communicate with the devices of fast mode or above.)
* Standard-mode devices include basic sensors such as RTC, temperature sensor, etc. (The devices that does not require fast communication.)

### Fast Mode

* In fast mode, devices can transmit and receive data up to 400 Kbps.
* Fast-mode devices are backward-compatible and can communicate with standard-mode devices in a 0-100 Kbps I2C bus system.
* Standard-mode devices, however, are not forward compatible. So, they should not be incorporated in a fast-mode I2C bus system as they cannot follow the faster transfer rate which in turn can lead to unpredictable states.
* To achieve data transfer rate up to 400 Kbps, you must put the I2C device in the fast mode.



## I2C Protocol

* I2C communication is always initiated by the master generating the "start condition" on the SDA line.

* Every byte put on the SDA line must be eight-bit long.
* Each byte must byte must be followed by an ACK bit.
* Data is transferred with the Most Significant Bit (MSB) first.



<img src="img/i2c-protocol-sda.png" alt="i2c-protocol-sda" width="800">



* Slave with matching address will send an ACK bit.

* Upon detecting the ACK bit, the master will read/write one byte of data depending on the value of the read/write bit.

* If the master wants to send more data, it will send more data like this.

  If not, the master will generate the "stop condition" and will close the communication.

* Once the stop condition is generated, the bus is released and the master no longer has the control over the bus. Other masters can initiate the communication.

### START & STOP Conditions

* All transactions begin with a START (S) and are terminated by a STOP (P).

* A HIGH-to-LOW transition on the SDA line while SCL is HIGH defines a START condition.

  A LOW-to-HIGH transition on the SDA line while SCL is HIGH defines a STOP condition.

  > Good source of interview question!



<img src="img/i2c-bus-protocol.png" alt="i2c-bus-protocol" width="800">



* Points to Remember

  * START and STOP conditions are always generated by the master. The bus is considered to be busy after the START condition.

  * The bus will be free again after certain amount of time from the STOP condition.

  * When the bus is free, another master (if present) can claim the bus.

  * The bus stays busy if a repeated START (Sr) is generated instead of a STOP condition.

  * Most of the MCU's I2C peripherals support both master and slave modes. You don't need to configure the their modes because when the peripheral generates the START condition it automatically becomes the master and when it generates the STOP condition it goes back to the slave mode.

    Some devices (e.g., sensors) will always be in the slave mode while others (e.g., MCUs) are capable of being both the master and the slave.

### ACK

* The Acknowledge signal is defined as follows:

  The transmitter releases the SDA line during the acknowledge clock pulse so the receiver can pull the SDA line LOW and it remains stable LOW during the HIGH period of this clock pulse.



<img src="img/i2c-ack-nack.png" alt="i2c-ack-nack" width="550">



* The acknowledge takes place after every byte.

* The acknowledge bit allows the receiver to signal the transmitter that the byte was successfully received and another byte may be sent.

* The master generates all clock pulses, including the acknowledge 9^th^ clock pulse.

* When SDA remains HIGH during this 9^th^ clock pulse. this is defined as Not Acknowledge (NACK) signal.

  The master can then generate either a STOP condition to abort the transfer, or a repeated START condition to start a new transfer.

* An ACK from the master is an indication for the slave to send one more byte to the master.

  An ACK from the slave is an indication that the slave has received the data successfully.

### Data Validity

> Good source of interview question!



<img src="img/i2c-data-validity.png" alt="i2c-data-validity" width="550">



* The data on the SDA line must be stable during the HIGH period of the clock. **The HIGH or LOW state of the data line can only change when the clock signal on the SCL line is LOW.**

  The only exceptions are START condition (i.e., HIGH-to-LOW transition on the SDA line while SCL line is HIGH), and the STOP condition (i.e., LOW-to-HIGH transition on the SDA line while SCL line is HIGH).

* One clock pulse is generated for each data bit tranferred.

### Repeated START

* START again without a STOP
  * STOPping before the goal of the I2C communication has been achieved may introduce the risk of giving other potential masters to take over the bus.
  * By using the **repeated START** technique, an already started I2C communication can secure the bus until it completes its job.
* In a single-master single-slave scenario, repeated START is not necessarily advantageous since there are no other masters that can potentially take over the bus.

### I2C Protocol Example

* The following example shows the master requesting and reading 2 bytes of data from the register with the address 0x00 of the device with the address 0x48.

  (Can be improved by using the "repeated START" instead of STOPing and STARTing again!)



<img src="img/i2c-bus-protocol-2.jpeg" alt="i2c-bus-protocol-2" width="850">



* An ACK from the master is an indication for the slave to send one more byte to the master.
* An ACK from the slave is an indication that the slave has received the data successfully.



## Block Diagram



<img src="img/i2c-block-diagram.png" alt="i2c-block-diagram" width="700">



* SMBA protocol is optional and we will not cover it here.

* Since I2C communication is half-duplex, only one data register is necessary. 

* Tx

  The I2C driver code will write to the data register whose contents will in turn be copied to the data shift register. Then the data will be sent out to the bus through SDA line.

* Rx

  The data will come through the SDA line to the data shift register whose contents will in turn be copied to the data register. Then the I2C driver code will read from the data register.

* "Own address register" is used to store slave's address in case the device itself is in slave mode.

* "Clock control register (CCR)" must be configured in order to produce different clock frequency on the SCL pin.



## I2C Serial Clock (SCL) Control Settings

* In STM32F4x I2C peripheral, CR2 and CCR registers are used to control the I2C serial clock settings and other I2C timings like setup time and hold time.

  

  <img src="img/i2c-cr2.png" alt="i2c-cr2" width="800">

  > FREQ[5:0]: Peripheral clock frequency (Must be configured with the APB clock frequency value - I2C peripheral connected to APB).

  

  <img src="img/i2c-ccr.png" alt="i2c-ccr" width="800">

  

  <img src="img/i2c-clock-setting.png" alt="i2c-clock-setting" width="700">

  > Do not assume that `FREQ[5:0]` will always be 16. It depends on the user configuration.





## Clock Stretching

* Clock stretching is one of the most powerful feature of I2C protocol.
* Clock stretching means holding the clock to 0 or GND level.
* The moment clock is held at low, the whole interface pauses until clock is recovered to its normal operation level.
* Usage of clock stretching:
  * I2C devices, either Master or Slave, uses this feature to **slow down the communication** by stretching SCL to low, **which prevents the clock to rise high again** and the I2C communication stops for a while.
  * There may be situations where an I2C slave is not able to cooperate with the clock speed generated by the master and needs to slow down a little.
  * If slave needs time, then it takes the advantage of clock stretching, and by holding clock at low, it momentarily pauses the I2C operation.



## Pull-Up Resistance (R~p~) Calculation

* $R_p(min)$ is a function of $V_{CC}$, $V_{OL}(max)$, and $I_{OL}$:

  > $V_{CC}$ = HIGH-level output voltage
  >
  > $V_{OL}(max)$ = LOW-level output voltage (According to I2C specification 0V - 0.4V is considered LOW.)
  >
  > $I_{OL}$ = LOW-level output current

  $$
  R_p(min) = \frac{V_{CC} - V_{OL}(max)}{I_{OL}}
  $$



* The maximum pull-up resistance is a function of the maximum rise time ($t_r$):

  > $t_r$ = Rise time of both SDA and SCL signals
  >
  > $C_b$ = Capacitive load for each bus line

  $$
  R_p(max) = \frac{t_r}{0.8473 \times C_b}
  $$



* Checkout the following table lifted from *I2C-bus specification and user manual (UM10204)*.

<img src="img/i2c-characteristics-of-the-sda-and-scl-io-stages.png" alt="i2c-characteristics-of-the-sda-and-scl-io-stages" width="900">



<img src="img/i2c-characteristics-of-the-sda-and-scl-bus-lines-for-standard-fast-and-fast-mode-plus-i2c-bus-devices.png" alt="i2c-characteristics-of-the-sda-and-scl-bus-lines-for-standard-fast-and-fast-mode-plus-i2c-bus-devices" width="900">



## Rise ($t_r$) and Fall ($t_f$) Times

* $t_r$ is defined as the amount of time taken by the rising edge to reach 70% amplitude from 30% amplitude for either SDA and SCL.

  $t_f$ is defined as the amount of time taken by the falling edge to reach 30% amplitude from an amplitude of 70%.

* I2C specification cares about $t_r$ value and you have to respect it while calculating the pull-up resistance.

* Higher pull-up resistance (i.e., weak pull-up) increases $t_r$ value. (Not acceptable if $t_r$ crosses maximum limit mentioned in the specification.)

  Using very high pull-up resistance may cause the pin not able to cross the $V_{IH}$ limit (i.e., Input Level High Voltage).



<img src="img/i2c-high-pull-up-resistance-issue.png" alt="i2c-high-pull-up-resistance-issue" width="450">



* Lower pull-up resistance (i.e., strong pull-up) decreases $t_r$ value (desirable) but they also lead to higher current consumption (undesirable). 

* Significance of Rise Time:

  Something is becoming hurdle for SDA/SCL line to rise to VCC level and the hurdle is RC time constant, where R is the pull-up resistance and C is the bus capacitance. ( $t_r \propto RC$ )

* $t_r$ calculation:
  $$
  \frac {t_r(max)}{t_{PCLK1}} = (F_{PCLK1} \times t_r(max))+1
  $$



## I2C Bus Capacitance ($C_b$)

* Bus capacitance is a collection of individual pin capacitance with respect to GND, capacitance between the SDA and SCL, parasitic capacitance, capacitance added by the devices connected to the bus, bus length (wire length), dielectric material, etc.

* Bus capacitance limits how long your I2C wiring can be and how many devices can be connected to the bus.

  e.g., Longer bus length $\to$ higher capacitance

  e.g., More devices connected to the bus $\to$ more pin capacitance added to the system

* Capacitance prevents the voltage level on the signal line (data or clock) from changing abrubtly.
* Greater capacitance will make $t_r$ bigger.
* See the specification for more information about the maximum allowed bus capacitance.

### Exercise

* For fast-mode I2C communication with the following parameters, calculate the pull-up resistance. 

  $C_b$ = 150pF (e.g., 4 devices with pin capacitance of 10pF each, 10pF of bus capacitance)

  $V_{cc}$ = 3.3V

  **Solution:**
  $$
  R_p(min) = \frac{3.3 - 0.4}{3 \times 10^{-3}} = 966 \ohm
  $$

  $$
  R_p(max) = \frac{300 \times 10^{-9}}{0.8473 \times 150 \times 10^{-12}} = 2.3 k\ohm
  $$

  
  $$
  \therefore 966 \ohm \lt R_p \le 2.3 k\ohm
  $$
  

  Each value can be found in the I2C specification.





## I2C Master & Slave

### Services Provided by I2C Master & Slave

* Master
  * START
  * STOP
  * Read (Master receiver)
  * Write (Master transmitter)
* Slave
  * Send data (Slave transmitter)
  * Receive data (Slave receiver)

### Master

* **Master transmitter**

  

  <img src="img/i2c-transfer-sequence-diagram-for-master-transmitter.png" alt="i2c-transfer-sequence-diagram-for-master-transmitter" width="900">



* **Master receiver**

  

  <img src="img/transfer-sequence-diagram-for-master-receiver.png" alt="transfer-sequence-diagram-for-master-receiver" width="900">

  > Must read the DR when RxNE = 1. Without confirming the RxNE value, do NOT access the DR.
  >
  > Signal NACK after the last byte received to initiate the termination process.
  >
  > Reading the final 2 bytes of data:
  >
  > * 2nd to last byte (when len = 2)
  >   * ACK = 0
  >   * STOP = 1
  >   * Read DR
  >   * Decrement the length (i.e., len = 1)
  > * Last byte
  >   * Read DR

### Slave

* **Slave transmitter**

  

  <img src="img/i2c-transfer-sequence-diagram-for-slave-transmitter.png" alt="i2c-transfer-sequence-diagram-for-slave-transmitter" width="800">

  > Master's read transaction is also initiated by the master. (Generating START condition)
  >
  > From the slave's stand point the "Address phase" is address matching phase. The slave with matching address will ACK.
  >
  > When the slave ACKs, ADDR flag will get set (`EV1`) which means that the address has been matched. (In master, ADDR=1 means the address has been sent.) `EV1` will stretch the SCL low so the software must c;ear the ADDR flag.
  >
  > Then, TxE gets set (`EV3-1`) which means both the shift register and data register are empty. (i.e., Request for data). At this point, SW can load the data into DR and the data can be transmitted to the master. At every `EV3` the contents of SR will be written to DR. (TxE=1 will happen only when the master ACKs upon receiving a byte of data from slave.)
  >
  > If the master ACKs upon receiving a byte of data, it means that the master is expecting more data.
  >
  > If the master NACKs upon receiving a byte of data, it means that the master is not expecting any more data. Thus, the slave should shall stop sending data.



* **Slave receiver**

  

  <img src="img/i2c-transfer-sequence-diagram-for-slave-receiver.png" alt="i2c-transfer-sequence-diagram-for-slave-receiver" width="800">

  > Master's write transaction is also initiated by the master. (Generating START condition)
  >
  > From the slave's stand point the "Address phase" is address matching phase. The slave with matching address will ACK.
  >
  > When the slave ACKs, `ADDR` flag will get set (`EV1`) which means that the address has been matched. (In master, `ADDR=1` means the address has been sent.) `EV1` will stretch the SCL low so the software must clear the `ADDR` flag.
  >
  > The moment the `ADDR` flag is cleared, data will be received from the master. Then, only after slave ACKs, `RxNE` will get set. This will generate an event to the application so that SW can perform reading the data (DR).
  >
  > When the slave detects the STOP condition (i.e., setting of `STOPF` flag) the slave must conclude that the master has ended the write transaction. (This will happen only when the device is in slave receiver mode.)

* I2C slave mode callback events

  I2C driver must be capable of sending the following callback events to the slave application.

  

  <img src="img/i2c-slave-mode-callback-events.png" alt="i2c-slave-mode-callback-events" width="700">





## I2C Interrupts

* I2C interrupt requests



<img src="img/i2c-interrupt-requests.png" alt="i2c-interrupt-requests" width="800">



* I2C interrupts and IRQ numbers

  IRQ numbers are specific to MCUs. Check the vector table of the MCU's reference manual.

  

<img src="img/i2c-interrupt-mapping-diagram.png" alt="i2c-interrupt-mapping-diagram" width="800">





## I2C ISR Handling

* ISR1 (`I2C_EV_IRQHandling`)

  Interrupt handling for interrupts generated by I2C events

* ISR2 (`I2C_ER_IRQHandling`)

  Interrupt handling for interrupts generated by I2C errors



## I2C Errors

I2C driver should be able to catch the following errors and notify the application. Then, it is the application's reponsibility to take necessary action.

* **Bus Error**
  * This error occurs when the interface detects an SDA rising or falling edge while SCL is high, occuring in a non-valid position during a byte transfer.
* **Arbitration Loss Error**
  * This error occurs when the interface loses the arbitration of the bus to another master. (Can occur only in a multi-master environment)

* **ACK Failure Error**

  * Occurs when no ACK is returned for the byte sent. 

* **Overrun Error**

  * Occurs when (during reception) a new byte is received and the data register has not been read yet then the newly received byte gets lost.

  * In I2C, the overrun and underrun will not happen if clock stretching is enabled because in those conditions (i.e., both the Shift Register (SR) and Data Register (DR) are full $\to$ BTF=1) the clock will be held LOW and both communicating devices will enter the wait state. (The BTF bit will be cleared only when the software reads the DR.)

* **Underrun Error**

  * Occurs when (during transmission) a new byte should be sent and the data register has not been written yet then the same byte gets sent twice.
  * BTF flag in Tx and preventing underrun error
    * During Tx of a data byte, if TxE=1, then it means that the data register (DR) is empty.
    * And if the firmware has not written any byte to DR before SR becomes empty (previous byte transmission), then the BTF flag will be set and clock will be stretched to prevent the underrun. Without this mechanism, it is likely that the meaningless data left in DR may flow into SR, which is undesirable.
    * If RxNE=1, then it means that a new data is waiting in the DR, and if the firmware has not read the data byte yet before the SR is filled with another new data, then also the BTF flag will be set and clock will be stretched to prevent the overrun.

* **PEC Error**

  * Occurs when CRC mismatch is detected while the CRC feature is enabled.

* **Timeout Error**
  * Occurs when master or slave stretches the clock longer than the recommended amount of time defined in the reference manual.
