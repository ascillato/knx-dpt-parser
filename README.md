# knx-dpt-parser
Parser for KNX telegrams. 

If you do not know what KNX is,  
or do not know what group addresses are,  
**you are in the wrong repository**  

### If you're still here...
Here is how to use it:

You have a node project that gets unparsed KNX telegram data. Header etc. need already to be interpreted, we deal with the payload only.  
An example can be found in the *testbusmonitor.js* which is a simple group monitor which prints to stdout. There is a config variable on top to configure the connection to *knxd*  

You require at least the Decorder.decode
```javascript
var decode = require('knx-dpt-parser').decode;
```
Alternatively, you can use `convenienceDecode()` which adds some more easiness to DPT10 Time and DPT11 Date telegrams.  

```javascript
var decode = require('knx-dpt-parser').convenienceDecode;
```


Then in you code, if you have both, telegram payload and destination group address, you call the decoder

```javascript
result  = decode(payload, dpttype);
```
The result is an object, containing one child object for each contained DPT-format (most types just have one format), plus an 'subitems' entry with the number of formats contained, and a 'TypeText' entry specifying the type of the DPT.  
All format child objects contain a value field, containing the numeric value of the section (except for char/string types DPT4 & 16).  

Example for a simple DPT1.001 telegram: 

```
{'0': { value: 1, interpretation: 'True' }, TypeText: 'boolean', subitems: 1, value: 1 }
``` 
The last value field is available in the `convenienceDecode()` only.  

A sample of a date DPT11.001, also by `convenienceDecode()` to get the combined value field:  
```
 { '0': { value: null, interpretation: 'reserved' },
  '1': { value: 25, Unit: 'Day of month' },
  '2': { value: null, interpretation: 'reserved' },
  '3': { value: 2, Unit: 'Month' },
  '4': { value: null, interpretation: 'reserved' },
  '5': { value: 2017, Unit: 'Year' },
  TypeText: 'date',
  DPT: 'DPST-11-1',
  subitems: 6,
  value: '2017-02-25' 
 }
```
A sample of time DPT10.001

 
Enumerative types (such as HVAC control mode DPT20.102) have the text of the mode in 'interpretation'.

```
{ '0': { value: 3, interpretation: 'Economy', Name: 'HVACMode' },
  TypeText: 'HVAC mode',
  DPT: 'DPST-20-102',
  subitems: 1 }
```

The `dpttype` parameter can either be a string like 'DPT1.001' or 'DPST-1-1', in fact '1.1' would be sufficient. It could optimize the process if you convert all DPTs to harmonised objects before using them regulary. For that, use `dptobject = uniformDPT('string-1-1')` and pass that object later to decode. You have to pay attention to the way you require the package, of course.
  
  
 
