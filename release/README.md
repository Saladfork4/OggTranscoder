# Usage Guide #

## Installation ##
* Navigate to the /release/ folder.
* Copy the following files into your project: **libvorbisfile.a**, **libOggTranscoder.a**, **OggTranscoder.hpp**, **COPYING**. 
* Select **Copy Items as Needed**. If you wish to have them as references, you'll have to resolve the linker errors by adjusting the build setting search paths.
* If you still experience linker errors, be sure that libvorbisfile.a is *above* libOggTranscoder.a in the build phases.

## Usage ##
There is only one function available at the moment:
```C++
  static OggWav OggTranscoder::transcode(void *ogg, unsigned long bytes);
```
Pass in a buffer and the buffer size and it will output a struct with uncompressed PCM-WAVE data. It also stores the length of the data in bytes:
```C++
struct OggWav {
    unsigned long size = 0;
    void *data;
};
```

Here is sample code that will loads an ogg file from the computer and plays it through AVAudioPlayer.
```objective-c
/* Load the source ogg file */
NSMutableData *oggData = [[NSMutableData alloc] initWithContentsOfFile:@"Epoq-Lepidoptera.ogg"];
void *oggBuffer = [oggData mutableBytes];
size_t oggLength = [oggData length];
    
/* Transcode it to a wav file */
OggWav wav = OggTranscoder::transcode(oggBuffer, oggLength);
if (wav.size == 0) {
    NSLog(@"Error.");
    return;
}
    
/* 
    Extract the data from the wav struct
    and resign ownership to the NSData object.
 */
NSData *wave = [[NSData alloc] initWithBytesNoCopy:wav.data length:wav.size freeWhenDone:YES];
    
/* Pass the data to an audio player */
_audio = [[AVAudioPlayer alloc] initWithData:wave fileTypeHint:AVFileTypeWAVE error:nil];
if (!_audio) {
    NSLog(@"Error.");
    return;
}
[_audio play];
```