# DotPad 320
*This is not an official document for DotPad 320.*

*Some applications expect the device name broadcast to be 'DotPad_320',
You need to turn off/on bluetooth in Setting of your iPad*

- ServiceID: `49535343-FE7D-4AE5-8FA9-9FAFD205E455`
- CharacteristicUUID: `49535343-1E4D-4BD9-BA61-23C647249616`

## Message Structure

`{HEAD}{LENGTH}{DATA}{CHECKSUM}`

| Name     | Byte(s)      | Description                  |
|----------|--------------|------------------------------|
| HEAD     | 2            | `AA55`                       |
| LENGTH   | 2            | The total length the message |
| DATA     |              | The raw data                 |
| CHECKSUM | 1            | The CheckSum of data         |

### Calculate the CHECKSUM of the message
```javascript
/**
 * @param {String} data - data in hex
 * @return {String}
 */
function calculateSum(data) {
    let check = 0xA5;
    const bytes = hexToBytes(data);
    for (let i = 0; i < bytes.length; i++) {
        check = check ^ bytes[i];
    }

    return ('0' + (check & 0xFF).toString(16)).slice(-2);
}
function hexToBytes(hex) {
    const bytes = [];
    for (let c = 0; c < hex.length; c += 2) {
        bytes.push(parseInt(hex.substr(c, 2), 16));
    }
    return bytes;
}
```

## Braille Patterns

Please refer to [Braille Patterns on Wikipedia](https://en.wikipedia.org/wiki/Braille_Patterns)

## Display

There are 11 rows on the pad, 1 row on the bottom and 10 rows above, each has 10 cells.
You can control every line separately. The message is in the following
pattern: `{HEAD}{LENGTH}{ROW}0200{MODE}{START}{BRAILLES}{CHECKSUM}` 

| Name     | Byte(s)      | Description                     |
|----------|--------------|---------------------------------|
| HEAD     | 2            | `AA55`                          |
| LENGTH   | 2            | The total length the message    |
| ROW      | 1            | row id                          |
| MODE     | 1            | `00` for Graphic, `08` for Text |
| START    | 1            | Start cell id                   |
| BRAILLES |              | Brailles data                   |
| CHECKSUM | 1            | The CheckSum of data            |

*The row id for the bottom row is 0, and 1 - 10 from top to bottom for the 10 rows above.*

## Special Commands
*space in command is for readability*

| Command                   | Description          |
|---------------------------|----------------------|
| `AA55 0005 00 0100 00 A4` | get device name      |
| `AA55 0005 00 0000 00 A5` | get firmware version |
| `AA55 0005 00 0010 00 A4` | get hardware version |

## Key Events

*The command for LP + F1 and RP + F4, it seems to be two separate command combined.*

### Key Down Events

| Key | Command |
|----|----|
| LP          | `AA 55 00 09 00 03 12 00 00 04 00 00 B0`
| F1          | `AA 55 00 09 00 03 32 00 80 00 00 00 14`
| F2          | `AA 55 00 09 00 03 32 00 40 00 00 00 D4`
| F3          | `AA 55 00 09 00 03 32 00 20 00 00 00 B4`
| F4          | `AA 55 00 09 00 03 32 00 10 00 00 00 84`
| RP          | `AA 55 00 09 00 03 12 00 00 02 00 00 B6`
| LP + F1(1)  | `AA 55 00 09 00 03 32 00 80 00 00 00 14 AA 55 00 09 00 03 12 00 00 04 00 00 B0`
| LP + F1(2)  | `AA 55 00 09 00 03 12 00 00 04 00 00 B0 AA 55 00 09 00 03 32 00 80 00 00 00 14`
| F1 + F2     | `AA 55 00 09 00 03 32 00 C0 00 00 00 54`
| F2 + F3     | `AA 55 00 09 00 03 32 00 60 00 00 00 F4`
| F3 + F4     | `AA 55 00 09 00 03 32 00 30 00 00 00 A4`
| F4 + RP(1)  | `AA 55 00 09 00 03 32 00 10 00 00 00 84 AA 55 00 09 00 03 12 00 00 02 00 00 B6`
| F4 + RP(2)  | `AA 55 00 09 00 03 12 00 00 02 00 00 B6 AA 55 00 09 00 03 32 00 10 00 00 00 84`
| LP + RP     | `AA 55 00 09 00 03 12 00 00 06 00 00 B2`

### Key Up Events

| Key | Command |
|----|----|
| LP           | `AA 55 00 09 00 03 12 00 00 00 00 00 B4`
| F1           | `AA 55 00 09 00 03 32 00 00 00 00 00 94`
| F2           | `AA 55 00 09 00 03 32 00 00 00 00 00 94`
| F3           | `AA 55 00 09 00 03 32 00 00 00 00 00 94`
| F4           | `AA 55 00 09 00 03 32 00 00 00 00 00 94`
| RP           | `AA 55 00 09 00 03 12 00 00 00 00 00 B4`
| LP + F1(1)   | `AA 55 00 09 00 03 32 00 00 00 00 00 94 AA 55 00 09 00 03 12 00 00 00 00 00 B4`
| LP + F1(2)   | `AA 55 00 09 00 03 12 00 00 00 00 00 B4 AA 55 00 09 00 03 32 00 00 00 00 00 94`
| F1 + F2      | `AA 55 00 09 00 03 32 00 00 00 00 00 94`
| F2 + F3      | `AA 55 00 09 00 03 32 00 00 00 00 00 94`
| F3 + F4      | `AA 55 00 09 00 03 32 00 00 00 00 00 94`
| F4 + RP(1)   | `AA 55 00 09 00 03 32 00 00 00 00 00 94 AA 55 00 09 00 03 12 00 00 00 00 00 B4`
| F4 + RP(2)   | `AA 55 00 09 00 03 12 00 00 00 00 00 B4 AA 55 00 09 00 03 32 00 00 00 00 00 94`
| LP + RP      | `AA 55 00 09 00 03 12 00 00 00 00 00 B4`

## Sample Data

You can [download from here](./dotpad-sample.csv)
