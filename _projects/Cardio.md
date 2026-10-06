---
title: Analog Cardio-tachometer
status: open
tags: [Op-Amp, Low-pass Filter, Analog]
image: /assets/projects/cardio/preview_16x9.png
demo_video: /assets/projects/cardio/preview_16x9.png
layout: project
description: Heart rate measurement indicates the robustness of the human cardiovascular system. This mini-project presents a technique for measuring heart rate by detecting variations in blood volume inside an artery of the finger, caused by the pumping action of the heart. It consists of an infrared LED that emits an IR signal through the subject’s fingertip. Part of this infrared light is reflected by the blood cells. The reflected signal is detected by a photodiode sensor. The change in blood volume with each heartbeat produces a series of pulses at the photodiode output, whose amplitude is too small to be detected directly. Therefore, a high-gain, three-stage active low-pass filter and a comparator are designed using three operational amplifiers (op-amps) to filter and amplify the signal to an appropriate voltage level. The heart rate is displayed on a red LED.
order: 4
---

## Introduction

Heart rate measurement indicates the robustness of the human cardiovascular system. This mini-project presents a technique for measuring heart rate by detecting variations in blood volume inside an artery of the finger, caused by the pumping action of the heart. It consists of an infrared LED that emits an IR signal through the subject's fingertip. Part of this infrared light is reflected by the blood cells. The reflected signal is detected by a photodiode sensor. The change in blood volume with each heartbeat produces a series of pulses at the photodiode output, whose amplitude is too small to be detected directly. Therefore, a high-gain, three-stage active low-pass filter and a comparator are designed using three operational amplifiers (op-amps) to filter and amplify the signal to an appropriate voltage level. The heart rate is displayed on a red LED.

## SpO2 Sensor

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture1.jpg' | relative_url }}" alt="SpO2 sensor." class="alpha">
  <figcaption><strong>Figure 1</strong>. SpO2 sensor.</figcaption>
</figure>

The sensor used is an optical sensor that measures the change in blood volume at the fingertip with each heartbeat. The sensor unit consists of an infrared light-emitting diode (IR LED) and a photodiode, placed side by side as illustrated below. The IR diode emits infrared light into the fingertip (placed above the sensor unit), and the photodiode detects the portion of the light that is reflected. The intensity of the reflected light depends on the blood volume inside the fingertip. Thus, each heartbeat slightly changes the amount of reflected infrared light that can be detected by the photodiode. With proper signal conditioning, this small variation in the amplitude of the reflected light can be converted into a pulse. The pulses can then be counted by the comparator to determine the heart rate.

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture2.jpg' | relative_url }}" alt="SpO2 sensor." class="alpha">
  <figcaption><strong>Figure 2</strong>. Sensor functionality</figcaption>
</figure>


## Characteristics of a Red LED

The goal is to vary the voltage applied across the LED and examine its properties based on the results obtained. Referring to the simulation data:

<figure class="beta">
  <img src="{{ '/assets/projects/cardio/Picture3.png' | relative_url }}" alt="Red LED characterization results." class="alpha">
  <figcaption><strong>Figure 3</strong>. Red LED characterization results.</figcaption>
</figure>


Simulation of a red LED in LTspice:

<figure class="beta">
  <img src="{{ '/assets/projects/cardio/Picture4.png' | relative_url }}" alt="LED characterization simulation in LTspice." class="alpha">
  <figcaption><strong>Figure 4</strong>. LED characterization simulation in LTspice.</figcaption>
</figure>



According to the results, the forward voltage of a red diode is Vf = 1.6V.

## Filtering and Amplification

### 1st Stage - Filtering

Our goal is to build a current-to-voltage converter:

The photodiode generates current when it receives light, so it can be considered a current source; we add a resistor to convert it into a voltage source.

The resting heart rate of a normal person is 60 to 100 beats per minute when calm, which corresponds to a signal frequency of 1-1.67 Hz.

**Cut off frequency calculation:**

$$f_c = \frac{1}{2 \times \pi \times R \times C} = \frac{1}{2 \times \pi \times 820k \times 47nF} = 4.1Hz $$

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture6.png' | relative_url }}" alt="Schematic of the first stage" class="alpha">
  <figcaption><strong>Figure 5</strong>. Schematic of the first stage</figcaption>
</figure>

<figure class="beta">
  <img src="{{ '/assets/projects/cardio/Picture7.png' | relative_url }}" alt="Simulated signal of the first circuit." class="alpha">
  <figcaption><strong>Figure 6</strong>. Simulated signal of the first circuit.</figcaption>
</figure>

<figure class="beta">
  <img src="{{ '/assets/projects/cardio/Picture8.jpg' | relative_url }}" alt="Actual signal obtained." class="alpha">
  <figcaption><strong>Figure 7</strong>. Obtained signal from the analog circuit</figcaption>
</figure>

The maximum value of the signal is around 25 mV.

Signal with a decoupling capacitor (blue signal) to remove the DC components.

<figure class="beta">
  <img src="{{ '/assets/projects/cardio/Picture9.png' | relative_url }}" alt="1st stage output signal with (blue) and without (green) decoupling capacitor (simulation)." class="alpha">
  <figcaption><strong>Figure 8</strong>. 1st stage output signal with (blue) and without (green) decoupling capacitor (simulation).</figcaption>
</figure>

In practice, we used a 100 µF capacitor.

Yellow signal: capacitor output.

Green signal: capacitor input.

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture10.jpg' | relative_url }}" alt="Input and output signals of the decoupling capacitor." class="alpha">
  <figcaption><strong>Figure 9</strong>. Input and output signals of the decoupling capacitor.</figcaption>
</figure>

### 2nd Stage - Amplification

Gain-of-100 amplifier:

Op-amp configured as a non-inverting amplifier.

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture11.png' | relative_url }}" alt="Schematic of the second circuit." class="alpha">
  <figcaption><strong>Figure 10</strong>. Schematic of the second circuit.</figcaption>
</figure>

$$V_{out} = \left(1 + \frac{R_7}{R_6}\right) \times V_{in} = (1 + 100) = 101 \times V_{in}$$

Amplified signal:

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture12.png' | relative_url }}" alt="Amplified signal." class="alpha">
  <figcaption><strong>Figure 11</strong>. Amplified signal.</figcaption>
</figure>

Output signal with the amplified signal:

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture13.png' | relative_url }}" alt="Comparison between the non-amplified signal (blue) and the amplified signal (green)." class="alpha">
  <figcaption><strong>Figure 12</strong>. Comparison between the non-amplified signal (blue) and the amplified signal (green).</figcaption>
</figure>

In practice, the output signal will be 101 times larger than the input signal, such that:

$$V_{out} = 0.025 \times 101 = 2.5\,V$$

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture14.jpg' | relative_url }}" alt="Actual signal obtained for this part." class="alpha">
  <figcaption><strong>Figure 13</strong>. Actual signal obtained for this part.</figcaption>
</figure>

Heart rate calculation:

$$HR = \frac{1}{T} \times 60 = 1.5141 \times 60 = 90.8\,bpm$$

### 3rd Stage - Comparator

Comparator + peak detector:

If V+ > V-$, then Vs = +Vcc, otherwise Vs = -Vcc.

<figure class="beta">
  <img src="{{ '/assets/projects/cardio/Picture15.png' | relative_url }}" alt="Peak detector + comparator." class="alpha">
  <figcaption><strong>Figure 14</strong>. Peak detector + comparator.</figcaption>
</figure>

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture16.png' | relative_url }}" alt="Output signal with the comparator stage." class="alpha">
  <figcaption><strong>Figure 15</strong>. Output signal with the comparator stage.</figcaption>
</figure>


According to Figure 15, we can swap the inputs V+ and V- to get a positive square signal during the peak of the input signal. Also, to remove the negative parts of the square signal, it is enough to connect Vcc to ground.

<figure class="alpha">
  <img src="{{ '/assets/projects/cardio/Picture17.jpg' | relative_url }}" alt="Input and output signals of the comparator." class="alpha">
  <figcaption><strong>Figure 16</strong>. Input and output signals of the comparator.</figcaption>
</figure>


## Complete Schematic

<figure class="beta">
  <img src="{{ '/assets/projects/cardio/Picture18.png' | relative_url }}" alt="Final schematic." class="alpha">
  <figcaption><strong>Figure 17</strong>. Final schematic.</figcaption>
</figure>

## Conclusion

Through our mini-project, we succeeded in designing a heart rate monitor (cardio-tachometer) capable of measuring heart rate. Our work consists of several circuits designed to process the probe's output signal. We started by converting the probe's output signal into a voltage, followed by amplification since the initial signal was too weak. We also added a comparator to visualize the heartbeats. During this process, we ran into difficulties in choosing the values of the various components.
