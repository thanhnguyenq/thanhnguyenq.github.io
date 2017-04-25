```java
package com.testaudio.main;

import java.io.IOException;

import javax.sound.sampled.AudioFormat;
import javax.sound.sampled.AudioInputStream;
import javax.sound.sampled.AudioSystem;
import javax.sound.sampled.UnsupportedAudioFileException;

/**
 * <!-- https://mvnrepository.com/artifact/com.googlecode.soundlibs/mp3spi --> <dependency>
 * <groupId>com.googlecode.soundlibs</groupId> <artifactId>mp3spi</artifactId>
 * <version>1.9.5-1</version> </dependency>
 * 
 * @author nqthanh
 *
 */
public class MP3Decode {
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        MP3Decode player = new MP3Decode();
        player.play();
    }

    public void play() {
        try {
            AudioInputStream audioInputStream = AudioSystem.getAudioInputStream(this.getClass().getClassLoader().getResource("test.mp3"));
            AudioInputStream din = null;
            AudioFormat baseFormat = audioInputStream.getFormat();
            AudioFormat decodedFormat = new AudioFormat(AudioFormat.Encoding.PCM_SIGNED, baseFormat.getSampleRate(), 16, baseFormat.getChannels(),
                    baseFormat.getChannels() * 2, baseFormat.getSampleRate(), false);
            din = AudioSystem.getAudioInputStream(decodedFormat, audioInputStream);
            int bytesPerFrame = din.getFormat().getFrameSize();
            if (bytesPerFrame == AudioSystem.NOT_SPECIFIED) {
                bytesPerFrame = 1;
            }
            System.out.println(din.getFormat());
            System.out.println("Is Big Endian: " + din.getFormat().isBigEndian());
            System.out.println("Number of channel: " + din.getFormat().getChannels());

            int numBytes = 8000;
            byte[] audioBytes = new byte[numBytes];
            float[] samples = new float[numBytes / 2];
            int bufferReadResult;
            while ((bufferReadResult = audioInputStream.read(audioBytes, 0, numBytes)) > 0) {
                if (din.getFormat().getChannels() == 2) {
                    byte[] byteDataL = new byte[numBytes / 2];
                    byte[] byteDataR = new byte[numBytes / 2];

                    float[] samplesL = new float[numBytes / 4];
                    float[] samplesR = new float[numBytes / 4];

                    for (int i = 0, j = 0; i < bufferReadResult;) {
                        byteDataL[j] = audioBytes[i];
                        byteDataR[j] = audioBytes[i + 1];
                        j++;
                        i += 2;
                    }

                    /**
                     * Left
                     */

                    for (int i = 0, s = 0; i < bufferReadResult / 2;) {
                        int sample = 0;

                        sample |= byteDataL[i++] & 0xFF; // (reverse these two lines
                        sample |= byteDataL[i++] << 8; // if the format is big endian)


                        // normalize to range of +/-1.0f
                        samplesL[s++] = sample / 32768f;
                    }

                    float rms = 0f;
                    float peak = 0f;
                    for (float sample : samplesL) {

                        float abs = Math.abs(sample);
                        if (abs > peak) {
                            peak = abs;
                        }

                        rms += sample * sample;
                    }

                    rms = (float) Math.sqrt(rms / samples.length);

                    System.out.println("RMS Left: " + rms * 100);
                    System.out.println("Peak Left: " + peak * 100); // No need

                    /**
                     * Right
                     */

                    for (int i = 0, s = 0; i < bufferReadResult / 2;) {
                        int sample = 0;

                        sample |= byteDataR[i++] & 0xFF; // (reverse these two lines
                        sample |= byteDataR[i++] << 8; // if the format is big endian)


                        // normalize to range of +/-1.0f
                        samplesR[s++] = sample / 32768f;
                    }

                    rms = 0f;
                    peak = 0f;
                    for (float sample : samplesR) {

                        float abs = Math.abs(sample);
                        if (abs > peak) {
                            peak = abs;
                        }

                        rms += sample * sample;
                    }

                    rms = (float) Math.sqrt(rms / samples.length);

                    System.out.println("RMS Right: " + rms * 100);
                    System.out.println("Peak Right: " + peak * 100);
                    System.out.println("******************************");
                } else {
                    for (int i = 0, s = 0; i < bufferReadResult;) {
                        int sample = 0;

                        sample |= audioBytes[i++] & 0xFF; // (reverse these two lines
                        sample |= audioBytes[i++] << 8; // if the format is big endian)


                        // normalize to range of +/-1.0f
                        samples[s++] = sample / 32768f;
                    }
                    float rms = 0f;
                    float peak = 0f;
                    for (float sample : samples) {

                        float abs = Math.abs(sample);
                        if (abs > peak) {
                            peak = abs;
                        }

                        rms += sample * sample;
                    }

                    rms = (float) Math.sqrt(rms / samples.length);

                    System.out.println("RMS: " + rms * 100);
                    System.out.println("Peak: " + peak * 100);
                    System.out.println("******************************");
                } // END ELSE
            }
        } catch (UnsupportedAudioFileException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}

```
