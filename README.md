# CRC16 1.0.0

The CRC16 library implements [CRC-16](https://en.wikipedia.org/wiki/Cyclic_redundancy_check) (also known as CRC-16-ANSI or CRC-16-IBM) which is used in propular protocols such as [Modbus](https://github.com/electricimp/Modbus) and USB. The CRC16 class contains a single static function - `calculate ` - which computes the CRC16 of the data passed in.

**To add this library to your project, add `#require "CRC16.class.nut:1.0.0"` to the top of your device or agent code.**

You can view the library’s source code on [GitHub](https://github.com/electricimp/CRC16/tree/v1.0.0).

## Class Methods

### CRC16.calculate(*data, [start, end, initialValue]*)

The *calculate* method calculates the CRC16 value of *data* (a string or blob).

You can pass optional parameters (*start* and *end*) to specify the range - if no range is specified, the CRC will be computed against the entire data structure.

You may also pass an optional *initialValue*, which can be used for chaining together multiple CRC calculations.

```squirrel
#require "CRC16.class.nut:1.0.0"

// Verification Values from:
// http://www.lammertbies.nl/comm/info/crc-calculation.html
// Make sure to use "hex" option for non-typable characters

//Simple Test
local crc = CRC16.calculate("HelloCRC16");
server.log(format("0x%04X", crc)); //Should log 0x6AF6

//More advanced tests
tests <- [
    ["\x00\x00\x00\x00", 0x2400],
    [blob(4), 0x2400],
    ["\x00\x00\x00\x01", 0xE4C1],
    ["\x01\x23\x45\x67\x89\xAB\xCD\xEF", 0xF8E6],
    ["QWERTY", 0x7132]
]


foreach(pair in tests){
    if(CRC16.calculate(pair[0]) == pair[1]){
        server.log("Complete CRC Passed");
    }else{
        server.error("Complete CRC Failed");
    }

    //Compute the CRC from bytes 0-2, then from 2-end using the first part CRC as the initial value
    local firstPart = CRC16.calculate(pair[0], 0, 2);
    local secondPart = CRC16.calculate(pair[0], 2, null, firstPart);

    if(secondPart == pair[1]){
        server.log("Partial CRC Passed")
    }else{
        server.error("Partial CRC Failed")
    }
}
```

# License

The CRC16 class is licensed under [MIT License](https://github.com/electricimp/CRC16/tree/master/LICENSE).
