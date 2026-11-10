# Notes

## Sensor node → Gateway

For the senser node → gateway communication, sending bytes would probably be sufficient, though JSON offers easy-to-understand data.

### Design consideration

ESP32-NOW can sends up to 600 kb/s, far above what we need. [Source](https://esp32.com/viewtopic.php?t=43700#:~:text=I%27d%20say%20that%208%20channel%2024%20bit%20is%20way%20over%20what%20ESP%2DNow%20can%20handle%2C%20at%20least%20with%20v1%2E0%2E%20Conducting%20tests%20I%20can%20reliably%20push%20%7E600%20kBit%2Fs%2E)

### Possible message format – Byte packing

Simply concatenate all the bits/bytes together. Both the sensor node and the gateway would need to agree on a pre-set convention.

- MPU6050: 6-DOF, 16 bit per channel, so 96 bits per devices; 192 bits in total.
- FSR 402: ESP32's ADC is 12 bit in resolution; 24 bits in total.

When combined, the message size totals to 216 bits, or 27 bytes; far below the limit of ESP32-NOW.

### Possible message format – JSON

```jsonc
{
    // IMUs: either raw 16-bit values or converted units
    // AI will understand both anyway (pick one!)
    "imu1": {
        "accel": [0.06, -9.81, -0.25],
        "gyro": [0.02, -0.11, 0.03]
    },
    "imu2": {
        "accel": [-1.04, -8.97, 0.64],
        "gyro": [0.01, -0.23, 0.09]
    },
    // Force sensors: raw 12 bit ADC readings
    "force1": 1194,
    "force2": 562,
}
```

## Gateway → Cloud

```jsonc
{
    "action": "sleepBegin", // or sleepEnd

    "lightIntensity": 100,
    "tempCelsius": 25,
    "humidityPercent": 70,

    "timestamp": "2026-01-01T12:00:00+07:00", // ISO8601 format
}

```