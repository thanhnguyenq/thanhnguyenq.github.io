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
            AudioInputStream audioInputStream = AudioSystem.getAudioInputStream(this.getClass().getClassLoader().getResource("11k8bitpcm.wav"));
            int bytesPerFrame = audioInputStream.getFormat().getFrameSize();
            if (bytesPerFrame == AudioSystem.NOT_SPECIFIED) {
                bytesPerFrame = 1;
            }
            System.out.println(audioInputStream.getFormat());

            int numBytes = 1024;
            float sampleRate = audioInputStream.getFormat().getSampleRate();
            double[] data = new double[(numBytes * 2)];

            double[] re = new double[numBytes];
            double[] im = new double[numBytes];
            double[] magnitude = new double[numBytes / 2];

            DoubleFFT_1D fft = new DoubleFFT_1D(numBytes);
            byte[] audioBytes = new byte[numBytes];
            short[] temp = new short[numBytes];

            int bufferReadResult;
            while ((bufferReadResult = audioInputStream.read(audioBytes, 0, numBytes)) == 1024) {
                for (int i = 0; i < numBytes; i++) {// Hann window function
                    temp[i] = (short) (audioBytes[i] * 0.5 * (1.0 - Math.cos(2.0 * Math.PI * i / numBytes)));
                }

                for (int i = 0; i < numBytes && i < bufferReadResult; i++) {
                    data[2 * i] = (double) temp[i];
                    data[(2 * i) + 1] = 0.0;
                }

                fft.complexForward(data);

                for (int i = 0; i < numBytes / 2; i++) {
                    // real is stored in first part of array
                    re[i] = data[i * 2];
                    // imaginary is stored in the sequential part
                    im[i] = data[(i * 2) + 1];
                    // magnitude is calculated by the square root of (imaginary^2 + real^2)
                    magnitude[i] = Math.sqrt((re[i] * re[i]) + (im[i] * im[i]));
                }

                double max_magnitude = Double.NEGATIVE_INFINITY;
                int max_index = -1;
                // Get the largest magnitude peak
                for (int i = 0; i < numBytes / 2; i++) {
                    if (max_magnitude < magnitude[i]) {
                        max_magnitude = magnitude[i];
                        max_index = i;
                    }
                }
                // calculated the frequency
                // System.out.println((bufferReadResult / (double) numBytes) * (sampleRate * peak) /
                // numBytes);
                // System.out.println((sampleRate * peak) / numBytes);
                // System.out.println(max_index);
                System.out.println("Frequence: " + (sampleRate * max_index) / numBytes);
                System.out.println(closestKey((sampleRate * max_index) / numBytes));
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

    public static String[] notes = {"A", "A#", "B", "C", "C#", "D", "D#", "E", "F", "F#", "G", "G#"};

    public static String closestKey(double freq) {
        int key = closestKeyIndex(freq);
        if (key <= 0) {
            return null;
        }
        int range = 1 + (key - 1) / notes.length;
        return notes[(key - 1) % notes.length] + range;
    }

    public static int closestKeyIndex(double freq) {
        return 1 + (int) ((12 * Math.log(freq / 440) / Math.log(2) + 49) - 0.5);
    }
}
```
