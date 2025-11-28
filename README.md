# Cordova AI Voice Plugin

A Cordova plugin that provides Speech-to-Text (STT) and Text-to-Speech (TTS) capabilities for Android and iOS platforms.

## Features

- **Speech Recognition (Speech-to-Text)**: Convert spoken words into text
- **Speech Synthesis (Text-to-Speech)**: Convert text into spoken audio
- **Auto-stop recording**: Optionally stop recording after silence detection
- **Silent mode detection**: Prevents TTS when device is muted

## Supported Platforms

- Android
- iOS (13+)

## Installation

```bash
cordova plugin add https://github.com/os-adv-dev/cordova-aivoice-plugin.git


Or install from a local path:

```bash
cordova plugin add /path/to/cordova-aivoice-plugin
```

## Permissions

The plugin automatically configures the required permissions:

### Android
- `RECORD_AUDIO` - Required for speech recognition

### iOS
- `NSMicrophoneUsageDescription` - Required for microphone access
- `NSSpeechRecognitionUsageDescription` - Required for speech recognition

## API Reference

### startListening(success, error, autoStopRecording)

Starts listening for speech input and converts it to text.

**Parameters:**
- `success` (Function): Callback function that receives the recognized text
- `error` (Function): Callback function that receives error messages
- `autoStopRecording` (Boolean): If `true`, automatically stops recording after detecting silence (2 seconds)

**Example:**
```javascript
cordova.plugins.CdvAiVoice.startListening(
    function(recognizedText) {
        console.log("Recognized: " + recognizedText);
    },
    function(error) {
        console.error("Error: " + error);
    },
    true // Auto-stop after silence
);
```

### stopListening(success, error)

Manually stops the speech recognition.

**Parameters:**
- `success` (Function): Callback function called when stopping is successful
- `error` (Function): Callback function that receives error messages

**Example:**
```javascript
cordova.plugins.CdvAiVoice.stopListening(
    function() {
        console.log("Stopped listening");
    },
    function(error) {
        console.error("Error: " + error);
    }
);
```

### speak(success, error, text)

Converts text to speech and plays it through the device speaker.

**Parameters:**
- `success` (Function): Callback function called when speech is finished
- `error` (Function): Callback function that receives error messages
- `text` (String): The text to be spoken

**Example:**
```javascript
cordova.plugins.CdvAiVoice.speak(
    function() {
        console.log("Speech finished");
    },
    function(error) {
        console.error("Error: " + error);
    },
    "Hello, how can I help you today?"
);
```

## Error Handling

### Silent Mode Detection

The plugin detects when the device is in silent mode and returns an error instead of attempting to play audio that won't be heard:

- **Android**: Checks if the device ringer mode is set to Silent or Vibrate
- **iOS**: Checks if the output volume is set to zero

When silent mode is detected, the `speak` function will return the error:
```
"Device is in silent mode. Please disable silent mode to use text-to-speech."
```

### Common Errors

| Error | Description |
|-------|-------------|
| Permission denied | User denied microphone permission |
| Device is in silent mode | Device is muted (TTS only) |
| Text recognition unavailable | Speech recognition service not available |
| Invalid argument | Invalid or missing text parameter for TTS |

## Usage Example

```javascript
document.addEventListener('deviceready', function() {

    // Start listening for voice input
    document.getElementById('startBtn').addEventListener('click', function() {
        cordova.plugins.CdvAiVoice.startListening(
            function(text) {
                document.getElementById('result').innerText = text;
                // Respond with TTS
                cordova.plugins.CdvAiVoice.speak(
                    function() { console.log('Response spoken'); },
                    function(e) { console.error(e); },
                    "You said: " + text
                );
            },
            function(error) {
                console.error(error);
            },
            true
        );
    });

    // Stop listening manually
    document.getElementById('stopBtn').addEventListener('click', function() {
        cordova.plugins.CdvAiVoice.stopListening(
            function() { console.log('Stopped'); },
            function(e) { console.error(e); }
        );
    });

}, false);
```

## Platform Notes

### Android
- Uses Android's native `SpeechRecognizer` for STT
- Uses `TextToSpeech` engine for TTS
- Language is set to `en-US` by default

### iOS
- Uses `SFSpeechRecognizer` framework for STT (requires iOS 13+)
- Uses `AVSpeechSynthesizer` for TTS
- Supports on-device speech recognition when available
- Language is set to `en-US` by default

## License

MIT License

## Authors

Paulo Camilo & Andre Grillo - OutSystems
