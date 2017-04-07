```java
package com.testaudio.main;

import java.io.IOException;
import javax.sound.sampled.AudioInputStream;
import javax.sound.sampled.AudioSystem;
import javax.sound.sampled.UnsupportedAudioFileException;

import org.jtransforms.fft.DoubleFFT_1D;

public class PlayAudio {

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        PlayAudio player = new PlayAudio();
        player.play();
    }

    public void play() {
        try {
            AudioInputStream audioInputStream = AudioSystem.getAudioInputStream(this.getClass().getClassLoader().getResource("11k16bitpcm.wav"));
            int bytesPerFrame = audioInputStream.getFormat().getFrameSize();
            if (bytesPerFrame == AudioSystem.NOT_SPECIFIED) {
                bytesPerFrame = 1;
            }
            System.out.println(audioInputStream.getFormat());
            float sampleRate = audioInputStream.getFormat().getSampleRate();

            int numBytes = 1024;
            double[] audioDataDoubles = new double[(numBytes * 2)];

            double[] re = new double[numBytes];
            double[] im = new double[numBytes];
            double[] magnitude = new double[numBytes];

            DoubleFFT_1D fft = new DoubleFFT_1D(numBytes);
            byte[] audioBytes = new byte[numBytes];

            int bufferReadResult;
            while ((bufferReadResult = audioInputStream.read(audioBytes)) != -1) {
                System.out.println((bufferReadResult / (double) numBytes));
                for (int i = 0; i < numBytes && i < bufferReadResult; i++) {
                    audioDataDoubles[2 * i] = (double) audioBytes[i] / 32768.0; // signed 16 bit
                                                                                // 32768.0
                    audioDataDoubles[(2 * i) + 1] = 0.0;
                }
                fft.complexForward(audioDataDoubles);
                for (int i = 0; i < numBytes; i++) {
                    // real is stored in first part of array
                    re[i] = audioDataDoubles[i * 2];
                    // imaginary is stored in the sequential part
                    im[i] = audioDataDoubles[(i * 2) + 1];
                    // magnitude is calculated by the square root of (imaginary^2 + real^2)
                    magnitude[i] = Math.sqrt((re[i] * re[i]) + (im[i] * im[i]));
                }

                double peak = -1.0;
                // Get the largest magnitude peak
                for (int i = 0; i < numBytes; i++) {
                    if (peak < magnitude[i])
                        peak = magnitude[i];
                }
                // calculated the frequency
                System.out.println((bufferReadResult / (double) numBytes) * (sampleRate * peak) / numBytes);
                System.out.println("-----------------------------------------------");
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
