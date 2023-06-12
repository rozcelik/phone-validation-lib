## What is phone validation?

Phone validation is the process of checking if a phone number in your database is accurate and valid. It clarifies if a number is active and able to receive calls and texts. It can distinguish real phone numbers from fake ones.

## Install
```
npm install phone-validation-lib

// or

yarn add phone-validation-lib
```

## Usage

```javascript
const {phone-validation-lib} = require('phone-validation-lib');

// or

import {phone-validation-lib} from 'phone-validation-lib';
```

### 1. Simple usage

```javascript
phone('+852 6569-8900');
// { isValid: true, phoneNumber: '+85265698900',  countryIso2: 'HK', countryIso3: 'HKG', countryCode: '+852' }
```

### 2. With Country

```javascript
phone('(817) 569-8900', {country: 'USA'});
// { isValid: true, phoneNumber: '+18175698900', countryIso2: 'US', countryIso3: 'USA', countryCode: '+1'}

phone('(817) 569-8900', {country: 'HKG'});
// { isValid: false }
// not a valid HKG mobile phone number

phone('+1(817) 569-8900', {country: 'HKG'});
// { isValid: false }
// not a valid HKG mobile phone number

phone('6123-6123', {country: 'HKG'});
// { isValid: true, phoneNumber: '+85261236123', countryIso2: 'HK', countryIso3: 'HKG', countryCode: '+852' }
```

### 3. Without country code and no phone prefix

If both country code and country phone prefix are not provided, the phone number will be treated as USA or Canada by default.

```javascript
phone('(817) 569-8900');
// { isValid: true, phoneNumber: '+18175698900', countryIso2: 'US', countryIso3: 'USA', countryCode: '+1' }

phone('(817) 569-8900', {country: ''});
// { isValid: true, phoneNumber: '+18175698900', countryIso2: 'US', countryIso3: 'USA', countryCode: '+1' }

phone('780-569-8900', {country: null});
// { isValid: true, phoneNumber: '+17805698900', countryIso2: 'CA', countryIso3: 'CAN', countryCode: '+1' }
// 780 is a Canada phone prefix

phone('6123-6123', {country: null});
// { isValid: false }
// as default country is USA / CAN and the phone number does not fit such countries' rules
```

### 4. With country code / phone prefix, but no `+` sign

Even you input a valid phone number with a valid prefix, if there is no plus sign, it will not work as expected:

```javascript
phone('85291234567');
// or
phone('85291234567', {country: null});

// { isValid: false }
```

`852` is a valid Hong Kong phone prefix, and `91234567` is a valid Hong Kong mobile phone number.
However, there is no plus sign provided, the module will assume the phone number is a USA or Canada phone number, 
hence no result will be found.   

If you know you have provided country phone prefix, make sure you also provide a plus sign:

```javascript
phone('+85291234567');
// or
phone('+85291234567', {country: null});

// { isValid: true, phoneNumber: '+85291234567', countryIso2: 'HK', countryIso3: 'HKG', countryCode: '+852' }
```

or, if you know the country, and only want to reformat the phone number to E.164 format:

```javascript
phone('91234567',  {country: 'HKG'})
// { isValid: true, phoneNumber: '+85291234567', countryIso2: 'HK', countryIso3: 'HKG', countryCode: '+852' }
```

### 5. Skipping phone number initial digit checking

If you want to skip phone number initial digit checking, set `validateMobilePrefix` to false:

```javascript
phone('+(852) 2356-4902');
// { isValid: false }
// '2' is a Hong Kong landline phone number prefix, not a valid mobile phone number prefix

phone('+(852) 2356-4902', {validateMobilePrefix: true});
// { isValid: false }
// same as above, default value of validateMobilePrefix = true

phone('+(852) 2356-4902', {validateMobilePrefix: false});
// { isValid: true, phoneNumber: '+85223564902', countryIso2: 'HK', countryIso3: 'HKG', countryCode: '+852' }
// skipping mobile prefix checking
```

With `validateMobilePrefix` set to `false`, the initial digit checking logic will be disabled completely, even you enter a phone number start with a non-exist digit:

```javascript
phone('+(852) 0356-4902', {validateMobilePrefix: false});
// { isValid: true, phoneNumber: '+85203564902', countryIso2: 'HK', countryIso3: 'HKG', countryCode: '+852' }
// even the phone number start with `0` is not a valid landline phone number
```
Note that the module does not have the capability to determine if the prefix is a valid `landline` prefix number.

### 6. Trunk Code Detection Logic

For some phone numbers, such as this sample UK phone number:

```
+44 07911 123456
```

There is a trunk code `0` after the country code `+44` so that it is unable to match any correct country.

Hence the module will try to remove 1 digit after the country code,

and try to detect:

```
+44 7911 123456
```

and it would become a valid UK phone number now.

```javascript
phone('+4407911 123456')
// { isValid: true, phoneNumber: '+447911123456', countryIso2: 'GB', countryIso3: 'GBR', countryCode: '+44' }
```

If you want to disable this behavior, 
please set `strictDetection` to `true`:

```javascript
phone('+4407911 123456', {strictDetection: true})
// { isValid: false }
```
## License

This project is licensed under the [MIT license](https://github.com/AfterShip/phone/blob/master/LICENSE).
