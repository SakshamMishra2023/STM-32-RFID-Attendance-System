# STM32 RFID Reader Project

A student attendance system using STM32 microcontroller and RC522 RFID module to detect and identify student cards.

## Overview

This project implements an RFID-based attendance system that can recognize two specific student cards and provide audio/visual feedback when a valid card is detected. The system uses UART communication to send attendance messages and controls a buzzer for audio feedback.

## Hardware Components

- **STM32 Microcontroller** (STM32F1xx series)
- **RC522 RFID Module** - 13.56MHz RFID reader
- **Buzzer** - Connected to GPIO PB1 for audio feedback
- **UART Interface** - For serial communication (115200 baud rate)
- **SPI Interface** - For RC522 communication

## Features

- **Dual Student Recognition**: Identifies two specific RFID cards
- **Duplicate Detection**: Prevents multiple readings of the same card
- **Audio Feedback**: Buzzer activation for 2 seconds on valid card detection
- **Serial Output**: UART messages for attendance logging
- **Real-time Processing**: Continuous card scanning with 200ms intervals

### RC522 RFID Module
<!-- Insert RC522 connection diagram here -->

| RC522 Pin | STM32 Pin | Function |
|-----------|-----------|----------|
| SDA/SS    | PB9       | SPI Slave Select |
| SCK       | PA5       | SPI Clock |
| MOSI      | PA7       | SPI Master Out |
| MISO      | PA6       | SPI Master In |
| IRQ       | -         | Not used |
| GND       | GND       | Ground |
| RST       | PB0       | Reset |
| 3.3V      | 3.3V      | Power Supply |

### Buzzer Connection
<!-- Insert buzzer connection diagram here -->

| Component | STM32 Pin | Function |
|-----------|-----------|----------|
| Buzzer    | PB1       | Audio Output |

### UART Connection
<!-- Insert UART connection diagram here -->

| UART Pin | STM32 Pin | Function |
|----------|-----------|----------|
| TX       | PA2       | Transmit |
| RX       | PA3       | Receive |

## Circuit Diagram

<!-- Insert complete circuit diagram here -->

## Project Structure

```
STM32_RFID_Project/
├── Core/
│   ├── Inc/
│   │   ├── main.h
│   │   └── RC522.h
│   └── Src/
│       ├── main.c
│       └── RC522.c
├── Drivers/
├── README.md
└── .ioc file
```

## Software Configuration

### Peripherals Used

1. **SPI1**: Communication with RC522 module
   - Mode: Master
   - Data Size: 8-bit
   - Clock Polarity: Low
   - Clock Phase: 1st Edge
   - Baud Rate Prescaler: 8

2. **USART2**: Serial communication
   - Baud Rate: 115200
   - Word Length: 8 bits
   - Stop Bits: 1
   - Parity: None

3. **TIM2**: Timer for RC522 operations
   - Prescaler: 71
   - Period: 65535

4. **GPIO**: 
   - PB1: Buzzer control (Output)
   - PB0, PB9, PB14: RC522 control pins

## Registered RFID Cards

The system recognizes two specific RFID cards:

### Student 1
- **Card ID**: `227, 32, 69, 53, 24` (decimal)
- **Hex ID**: `E3 20 45 35 18`
- **Message**: "STUDENT 1 proxy received"

### Student 2
- **Card ID**: `35, 32, 83, 245, 174` (decimal)
- **Hex ID**: `23 20 53 F5 AE`
- **Message**: "STUDENT 2 proxy received"

## System Behavior

### Card Detection Flow

1. **Continuous Scanning**: System continuously scans for RFID cards
2. **Card Recognition**: When a card is detected, its UID is compared with registered cards
3. **Duplicate Prevention**: System tracks the last detected card to prevent multiple readings
4. **Feedback Generation**: 
   - Valid card triggers UART message
   - Buzzer activates for 2 seconds
   - GPIO PB1 goes HIGH during buzzer activation

### Serial Output

The system outputs the following messages via UART:

```
RFID Reader Started
STUDENT 1 proxy received
STUDENT 2 proxy received
```

## Setup Instructions

### Development Environment

1. **STM32CubeIDE** or **Keil MDK**
2. **STM32CubeMX** for configuration
3. **STM32 HAL Library**

### Build and Flash

1. Import the project into your IDE
2. Build the project
3. Connect ST-Link debugger
4. Flash the program to STM32

### Testing

<!-- Insert testing setup images here -->

1. Power on the system
2. Open serial monitor (115200 baud)
3. Present registered RFID cards to the reader
4. Verify UART output and buzzer operation

## Troubleshooting

### Common Issues

**No Card Detection**
- Check SPI connections between STM32 and RC522
- Verify power supply (3.3V) to RC522
- Ensure proper antenna connection

**Incorrect Card Reading**
- Check SPI clock frequency
- Verify RC522 reset pin connection
- Ensure cards are within reading range (2-3cm)

**No Serial Output**
- Verify UART connections
- Check baud rate settings (115200)
- Ensure proper ground connection

**Buzzer Not Working**
- Check GPIO PB1 connection
- Verify buzzer polarity
- Test GPIO output with multimeter

## Code Modifications

### Adding New Students

To add more students, modify the card detection logic in `main.c`:

```c
// Add new card ID
else if(sNum[0] == XX && sNum[1] == XX && sNum[2] == XX && sNum[3] == XX && sNum[4] == XX)
{
    currentCard = 3; // New student ID
    if(lastCardDetected != 3)
    {
        validCardDetected = 1;
        HAL_UART_Transmit(&huart2, (uint8_t*)"STUDENT 3 proxy received\r\n", 26, HAL_MAX_DELAY);
        HAL_GPIO_WritePin(GPIOB, GPIO_PIN_1, GPIO_PIN_SET);
    }
}
```

### Customizing Buzzer Duration

Change the delay value in the main loop:

```c
if(validCardDetected)
{
    HAL_GPIO_WritePin(GPIOB, GPIO_PIN_1, GPIO_PIN_SET);
    HAL_Delay(3000); // Change to 3 seconds
}
```

## Performance Specifications

- **Reading Range**: 2-3 cm
- **Response Time**: ~200ms
- **Operating Frequency**: 13.56 MHz
- **Card Types Supported**: MIFARE Classic, MIFARE Ultralight
- **Power Consumption**: ~50mA (active mode)

## Future Enhancements

- [ ] Database integration for attendance logging
- [ ] SD card storage for offline data
- [ ] Real-time clock for timestamping
- [ ] Multiple reader support
- [ ] Wireless connectivity (Wi-Fi/Bluetooth)

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## Support

For technical support or questions:
- Create an issue in the repository
- Contact: [saksham2684@gmail.com]

## Acknowledgments

- STMicroelectronics for HAL library
- RC522 library contributors
- STM32 community for support and resources

---

**Note**: This project is designed for educational purposes and basic attendance tracking. For production use, consider implementing additional security measures and error handling.
