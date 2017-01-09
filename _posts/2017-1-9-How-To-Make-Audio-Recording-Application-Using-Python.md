---
layout: post
title: How to make Audio Recording application using Python
excerpt: "In this quick tutorial I'm gonna show you how to make a tiny Audio Recording script using Python.
First of all we need a module called `pyaudio` to manage this process."
date: 2016-01-09
---
In this quick tutorial I'm gonna show you how to make a tiny Audio Recording script using Python.
First of all we need a module called `pyaudio` to manage this process.

We're going to use `Pip` to install the module. Make sure you set `pip` in your environment path variable.
You can set it using this command:
```Bash
SET PATH=%PATH%;C:\Python27\Scripts
```
Now execute this command to install `pyaudio` module:
```Bash
pip install pyaudio
```

And now we're ready to go further.

We're going to create a new python script and import `pyaudio` module and `wave` module which is
already installed with Python.
```Python
import pyaudio
import wave
```
Now we will define our basic variables.
```Python
CHUNK = 1024
FORMAT = pyaudio.paInt16
CHANNELS = 2
RATE = 44100
```
I'm not a sounds expert but let me explain some of these variables.
`CHUNK` is simply how many bytes a piece of sound will have. The chunk is like a buffer, so therefore each buffer will contain 1024 samples, which you can then either keep or throw away. We use CHUNKS of data, instead of a continuous amount of audio because of processing power. (Let's assume this was a continuous flow of data being received from a Microphone and is being recorded and saved) then it would just eat up the processor, thus causing potential crashes. In terms of Raspberry Pi / Arduino development (Where the RAM is very small) CHUNKING the data like this makes the stream flow easier and thus prevents memory leaks.

Let's assume that you wanted to implement an algorithm for determining whether something is speech, or, is just noise. How would/could you do this using a constant stream flow of sound data? It would be very difficult. Therefore, by storing this into an array (or list in our case) you can perform analysis on this data, for example RMS. Then you could have some threshold to determine if you want to keep the data, or the data is no good.

What if we increase the number of chunks? Generally you can say that increasing the chunk size will result in more delay because you would have to wait until the amounts of samples are ready at your device you are reading from. However this only is important if you have some real-time output of the signal. For example, if you read from a microphone, apply an effect, and want to play it back via the speakers. If you choose a chunk size of 22050, you will have to wait half a second until you actually hear the output. So generally lower chunks are preferred in realtime-systems. For recording it is not necessary.
`RATE` variable says how many samples are taken per second. Its unit is Hz = samples/second


Anyway, That's all I could know about these variable. Let's continue coding the script.

The next thing we'll ask the user for the final output file name then we'll set it to a variable
contains the name and the extension `wav`
```Python
filename = raw_input('* Enter the output file name you want: ')
WAVE_OUTPUT_FILENAME = filename + ".wav"
```
Now we will initiat a `PyAudio` object to start recording.
```Python
p = pyaudio.PyAudio()
stream = p.open(format=FORMAT,
                channels=CHANNELS,
                rate=RATE,
                input=True,
                frames_per_buffer=CHUNK)
print("* recording")
```
Now we're making a list for the frames we're getting from the user. Then we will make an infinite loop to read the stream and append it to our frames list.
And we'll use `except KeyboardInterrupt` so whenever the user press `CTRL+C` the loop will break.
```Python
frames = []
try:
    while True:
        data = stream.read(CHUNK)
        frames.append(data)
except KeyboardInterrupt:
    pass
print("* done recording")
```
Now we're going to stop the stream, close it and terminate `PyAudio` object.
```Python
stream.stop_stream()
stream.close()
p.terminate()
```
Then we will use `wave` module to create a new wav file and write the data we got into it like the following code.
```Python
#creates a new wav file with the file name the user entered at the beginning
wf = wave.open(WAVE_OUTPUT_FILENAME, 'wb')
#sets the channels number to our channels variable we defined at the beginning
wf.setnchannels(CHANNELS)
#sets the format using our FORMATE variable we defined at the beginning
wf.setsampwidth(p.get_sample_size(FORMAT))
#sets the rate our RATE variable we defined at the beginning
wf.setframerate(RATE)
#writes the frames we recorded
wf.writeframes(b''.join(frames))
#closes the object
wf.close()
```

This script is available in my [__GitHub Repository__](https://github.com/lilessam/PyRecorder)

For any questions to hesitate to send a comment below.
