# PULSE-CODE-MODULATION-AND-DELTA-MODULATION

## AIM:
To write Python programs for the modulation and demodulation of Pulse Code Modulation (PCM) and Delta Modulation (DM), and observe their corresponding waveforms.

## TOOLS REQUIRED:

### SOFTWARE:
- Python 3.x
- Google Colab / Jupyter Notebook
- NumPy
- Matplotlib
- SciPy

## THEORY:

### 1. Pulse Code Modulation (PCM)
Pulse Code Modulation is a digital modulation technique in which an analog signal is sampled, quantized, and encoded into binary form. The PCM process consists of three main steps:

- Sampling: Converts the continuous-time signal into discrete-time samples.
- Quantization: Maps the sampled amplitudes to predefined discrete levels.
- Encoding: Converts the quantized values into binary codewords.

In PCM demodulation, the binary data is decoded to obtain the quantized signal, which is then reconstructed using a low-pass filter.

### 2. Delta Modulation (DM)
Delta Modulation is a simplified form of differential pulse code modulation in which only the difference between consecutive signal samples is encoded.

It uses a single bit to indicate whether the signal amplitude increases or decreases.

- If the input signal is greater than the previous reconstructed sample, the output bit is 1.
- If the input signal is smaller than the previous reconstructed sample, the output bit is 0.

In demodulation, the encoded bits are integrated to reconstruct the signal. A low-pass filter is then used to smooth the reconstructed waveform.

## PROGRAM:

### 1. PULSE CODE MODULATION (PCM)
```python
    import numpy as np
    import matplotlib.pyplot as plt

    # Parameters
    sampling_rate = 5000
    frequency = 50
    duration = 0.1
    quantization_levels = 32

    # Generate Time Vector
    t = np.linspace(0, duration,
                    int(sampling_rate * duration),
                    endpoint=False)

    # Generate Message Signal
    message_signal = np.sin(2 * np.pi * frequency * t)

    # Generate Clock Signal
    clock_signal = np.sign(np.sin(2 * np.pi * 200 * t))

    # Quantization
    quantization_step = (
        max(message_signal) - min(message_signal)
    ) / quantization_levels

    quantized_signal = (
        np.round(message_signal / quantization_step)
        * quantization_step
    )

    # PCM Modulation
    pcm_signal = (
        (quantized_signal - min(quantized_signal))
        / quantization_step
    )

    pcm_signal = pcm_signal.astype(int)

    # PCM Demodulation
    demodulated_signal = quantized_signal

    # Plotting
    plt.figure(figsize=(12, 10))

    plt.subplot(5, 1, 1)
    plt.plot(t, message_signal, color='blue')
    plt.title("Message Signal (Analog)")
    plt.xlabel("Time [s]")
    plt.ylabel("Amplitude")
    plt.grid(True)

    plt.subplot(5, 1, 2)
    plt.plot(t, clock_signal, color='green')
    plt.title("Clock Signal")
    plt.xlabel("Time [s]")
    plt.ylabel("Amplitude")
    plt.grid(True)

    plt.subplot(5, 1, 3)
    plt.step(t, quantized_signal, color='red')
    plt.title("PCM Modulated Signal (Quantized)")
    plt.xlabel("Time [s]")
    plt.ylabel("Amplitude")
    plt.grid(True)

    plt.subplot(5, 1, 4)
    plt.plot(t, demodulated_signal,
             color='purple', linestyle='--')
    plt.title("PCM Demodulation Signal")
    plt.xlabel("Time [s]")
    plt.ylabel("Amplitude")
    plt.grid(True)

    plt.subplot(5, 1, 5)
    plt.step(t, pcm_signal, color='orange')
    plt.title("PCM Digital Output")
    plt.xlabel("Time [s]")
    plt.ylabel("Quantization Level")
    plt.grid(True)

    plt.tight_layout()
    plt.show()
```
### 2. DELTA MODULATION (DM)
```python
    import numpy as np
    import matplotlib.pyplot as plt
    from scipy.signal import butter, filtfilt

    # Parameters
    fs = 10000
    f = 10
    T = 1
    delta = 0.1

    t = np.arange(0, T, 1/fs)

    # Generate Message Signal
    message_signal = np.sin(2 * np.pi * f * t)

    # Delta Modulation Encoding
    encoded_signal = []
    dm_output = [0]
    prev_sample = 0

    for sample in message_signal:

        if sample > prev_sample:
            encoded_signal.append(1)
            dm_output.append(prev_sample + delta)

        else:
            encoded_signal.append(0)
            dm_output.append(prev_sample - delta)

        prev_sample = dm_output[-1]

    # Delta Demodulation
    demodulated_signal = [0]

    for bit in encoded_signal:

        if bit == 1:
            demodulated_signal.append(
                demodulated_signal[-1] + delta
            )

        else:
            demodulated_signal.append(
                demodulated_signal[-1] - delta
            )

    demodulated_signal = np.array(demodulated_signal)

    # Low-Pass Butterworth Filter
    def low_pass_filter(signal, cutoff_freq, fs, order=4):

        nyquist = 0.5 * fs
        normal_cutoff = cutoff_freq / nyquist

        b, a = butter(
            order,
            normal_cutoff,
            btype='low',
            analog=False
        )

        return filtfilt(b, a, signal)

    filtered_signal = low_pass_filter(
        demodulated_signal,
        cutoff_freq=20,
        fs=fs
    )

    # Plotting Results
    plt.figure(figsize=(12, 6))

    plt.subplot(3, 1, 1)
    plt.plot(t, message_signal,
             label='Original Signal', linewidth=1)
    plt.title("Original Message Signal")
    plt.legend()
    plt.grid()

    plt.subplot(3, 1, 2)
    plt.step(t, dm_output[:-1],
             label='Delta Modulated Signal', linewidth=1)
    plt.title("Delta Modulated Signal")
    plt.legend()
    plt.grid()

    plt.subplot(3, 1, 3)
    plt.plot(t, filtered_signal[:-1],
             label='Demodulated & Filtered Signal',
             linestyle='dotted',
             linewidth=1,
             color='red')
    plt.title("Delta Demodulated Signal")
    plt.legend()
    plt.grid()

    plt.tight_layout()
    plt.show()
```

## OUTPUT WAVEFORM:

### 1. PCM Output Waveform

<img width="1072" height="752" alt="image" src="https://github.com/user-attachments/assets/b2a01a21-fa2d-485d-83b5-c45670b1f3b7" />

Link To Full Screen Output:
https://colab.research.google.com/drive/1tZbAMxyhRaXMRy0qkoLdZa4q4afaYt76#scrollTo=PWJOwhSF5FXX&fullscreenOutput=true

### 2. Delta Modulation Output Waveform

<img width="1215" height="601" alt="image" src="https://github.com/user-attachments/assets/d9a32091-8c21-4456-a436-705d34b35886" />

Link To Full Screen Output:
https://colab.research.google.com/drive/1cUWosU1LM9KZtmSzvrhbJNzRCB3LjOYo#scrollTo=mWIx_gkP6tOy&fullscreenOutput=true

## RESULTS:

Thus, Pulse Code Modulation (PCM) and Delta Modulation (DM) were successfully implemented using Python. The modulation and demodulation processes were performed, and the corresponding input, modulated, and reconstructed waveforms were obtained and observed.
