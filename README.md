#SoundTouch进行音频处理
==================
整理自 自https://github.com/VladimirKulyk/SoundTouch-Android.git
原项目太老跑不动 ，后续改为cmake,不然开发体验太差

![效果图](https://github.com/meiliqin/AndroidSoundTouch/blob/main/image/IMG20210128_140706.png)



soundTouch的使用方法:

```java
//There are currently 16 track id's you can use (0-15), each one has a separate SoundTouch processor.

//Set your audio processing requirements: track id, channels, samplingRate, bytesPerSample,
//                              tempoChange (1.0 is normal speed), pitchChange (in semi-tones)

SoundTouch soundTouch = new SoundTouch(0, 2, 44100, 2, 1.0f, 2.0f);

//byte[] sizes are recommended to be 8192 bytes.

//put a byte[] of PCM audio in the sound processor:
soundTouch.putBytes(input);

//write output to a byte[]:
int bytesReceived = soundTouch.getBytes(output);

//after you write the last byte[], call finish().
soundTouch.finish();

//now get the remaining bytes from the sound processor.
int bytesReceived = 0;
do
{
    bytesReceived = soundTouch.getBytes(output);
    //do stuff with output.
} while (bytesReceived != 0)

//if you stop playing, call clear on the track to clear the pipeline for later use.
soundTouch.clearBuffer()
```
SoundStreamAudioPlayer.java 是一个支持可变速变调播放音频的播放器，基于MediaCodec做音轨解码，其使用方法如下：

```java
//The last two parameters are speed of playback (1.0 is 100% of original speed) and pitch adjustment in semi-tones.
SoundStreamAudioPlayer player = new SoundStreamAudioPlayer(0, f.getPath(), 1.0f, 1.0f);
new Thread(player).start();
player.start();
````

结束播放时，应调用stop释放资源：

```java
st.pause();
st.stop();
````
音频变速变调的另外一种思路是，对音频文件通过soundTouch处理后，重新编码为aac文件，任何播放器可以播放这个文件：
```java
SoundStreamFileWriter writer = new SoundStreamFileWriter(0, fileNameIn, fileNameOut, tempo, pitch);
new Thread(writer).start();
writer.start();
````

