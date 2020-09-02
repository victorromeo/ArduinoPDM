# PDM library

The PDM library allows you to use PDM (Pulse-density modulation) microphones, such as the on board MP34DT05 on the Arduino Nano 33 BLE Sense.

## ArduinoPDM Enhancement

Available sample rates include:
- 16000, 20000, 25000, 31250, 33333, 40000, 41667, 50000, 62500

This is achieved by fully implementing all PDM options:

| NRF_PDM_FREQ | Ratio 80 | Ratio 64             |
| ------------ | -------- | -------------------- |
| 1280         | 16000    | 20000                |
| 2000         | 25000    | 31250                |
| 2667         | 33333    | 41667 (actual 41671) |
| 3200         | 40000    | 50000                |
| 4000         | 50000    | 62500                |

## Usage

### begin()

#### Description

Initialize the PDM interface.

#### Syntax

PDM.begin(channels, sampleRate)

#### Parameters

channels: the number of channels, 1 for mono, 2 for stereo
sampleRate: the sample rate to use in Hz
Returns
1 on success, 0 on failure

#### Example

```cpp
…
  if (!PDM.begin(1, 16000)) {
    Serial.println("Failed to start PDM!");
    while (1);
  }

  setGain(0x50);
…
```

### end()

#### Description

De-initialize the PDM interface.

#### Syntax

PDM.end()

#### Parameters

None

#### Returns

Nothing

#### Example

```cpp
…
  if (!PDM.begin(1, 16000)) {
    Serial.println("Failed to start PDM!");
    while (1);
  }


  // …

  PDM.end();
…

```

### available()

#### Description

Get the number of bytes available for reading from the PDM interface. This is data that has already arrived and was stored in the PDM receive buffer.

#### Syntax

PDM.available()

#### Parameters

None

#### Returns

The number of bytes available to read

#### Example

```cpp
…
// buffer to read samples into, each sample is 16-bits
short sampleBuffer[256];

// number of samples read
volatile int samplesRead;

// …

  // query the number of bytes available
  int bytesAvailable = PDM.available();

  // read into the sample buffer
  PDM.read(sampleBuffer, bytesAvailable);
…
```

### onReceive()

#### Description

Set the callback function that is called when new PDM data is ready to be read.

#### Syntax

PDM.onReceive(callback)

#### Parameters

callback: function that is called when new PDM data is ready to be read

#### Returns

Nothing

#### Example

```cpp
…
// buffer to read samples into, each sample is 16-bits
short sampleBuffer[256];

// number of samples read
volatile int samplesRead;

// …

  // configure the data receive callback
  PDM.onReceive(onPDMdata);

  // initialize PDM with:
  // - one channel (mono mode)
  // - a 16 kHz sample rate
  if (!PDM.begin(1, 16000)) {
    Serial.println("Failed to start PDM!");
    while (1);
  }


  // …

void onPDMdata() {
  // query the number of bytes available
  int bytesAvailable = PDM.available();

  // read into the sample buffer
  Int bytesRead = PDM.read(sampleBuffer, bytesAvailable);

  // 16-bit, 2 bytes per sample
  samplesRead = bytesRead / 2;
}

…
```


### setGain()

#### Description

Set the gain value used by the PDM interface.

#### Syntax

PDM.setGain(gain)

#### Parameters

gain: gain value to use, 0 - 80 [hexadecimal 0x50] <strike> (0 - 255) </strike>, defaults to 20 if not specified.  

Note: Must be set after the begin operation call.

#### Returns

Nothing

#### Example

```cpp
…
  // initialize PDM with:
  // - one channel (mono mode)
  // - a 16 kHz sample rate
  if (!PDM.begin(1, 16000)) {
    Serial.println("Failed to start PDM!");
    while (1);
  }

  // optionally set the gain, defaults to 20
  PDM.setGain(30);
…
```

### setBufferSize()

#### Description

Set the buffer size (in bytes) used by the PDM interface. Must be called before PDM.begin(...), a default buffer size of 512 is used if not called. This is enough to hold 256 16-bit samples.

If 2 channels are used, 128 stereo samples are stored in 512 bytes, alternating left channel, right channel as signed int16 (short) pairs.

#### Syntax

PDM.setBufferSize(size)

#### Parameters

size: buffer size to use in bytes

Note: setBufferSize must be called before begin operation, to allocate memory to the buffer.

#### Returns

Nothing

#### Example

```cpp
…
  PDM.setBufferSize(1024);

  // initialize PDM with:
  // - one channel (mono mode)
  // - a 16 kHz sample rate
  if (!PDM.begin(1, 16000)) {
    Serial.println("Failed to start PDM!");
    while (1);
  }

…
```
