# audio_transcription
Transcribe audio files using Google's Automatic Speech Recognition (ASR) and Whisper ASR by OpenAI. It is designed to handle different audio sources and formats, providing flexibility and ease of use.

---

# Audio Transcription Notebook

## Table of Contents
1. [Introduction](#Introduction)
2. [Dependencies](#Dependencies)
3. [File Upload](#File-Upload)
4. [YouTube Audio Extraction](#YouTube-Audio-Extraction)
5. [Audio Preprocessing](#Audio-Preprocessing)
6. [Transcription Services](#Transcription-Services)
7. [Transcription](#Transcription)
8. [Output](#Output)

---

### Introduction
This notebook provides a comprehensive guide to transcribing audio files using Google's Automatic Speech Recognition (ASR) and Whisper ASR by OpenAI. It is designed to handle different audio sources and formats, providing flexibility and ease of use.

---

### Dependencies
The notebook utilizes several Python packages, including `pydub` for audio file manipulation, `speech_recognition` for Google ASR, and `whisper` for OpenAI's ASR. Make sure to install these before running the notebook.

```python
!pip install -— upgrade pytube
!pip install pydub
!pip install SpeechRecognition
!pip install git+https://github.com/openai/whisper.git -q 
```

---

### File Upload
This section includes a file uploader allowing you to upload audio files from your local machine. The uploaded file is then processed for transcription.

```python
# Upload an audio file

from google.colab import files
import os

# Upload the audio file
print("Please upload the audio file:")
uploaded = files.upload()

# Get the original name of the uploaded file
original_file_name = list(uploaded.keys())[0]

# Extract the file extension from the original file name
file_extension = os.path.splitext(original_file_name)[1][1:]

# No need to rename the file; use the original file name for processing
audio_path = original_file_name
```

---

### YouTube Audio Extraction
Alternatively, you can input a YouTube URL to extract audio directly for transcription. Note that extracting audio from YouTube may violate its terms of service.

```python
# Dowload audio from a Youtube video

# Importing Pytube library
import pytube

# Reading the Youtube video link
video = 'https://www.youtube.com/watch?v=-LIIf7E-qFI'  # Replace with Youtube URL
data = pytube.YouTube(video)

# Get the audio-only stream
audio = data.streams.get_audio_only()

# Download the audio and get the file path
audio_path = audio.download()

# Extract the file extension from the downloaded file
file_extension = os.path.splitext(audio_path)[1][1:]
```

---

### Audio Preprocessing
The audio file is split into manageable chunks using `pydub`. These chunks are saved temporarily for transcription.

```python
# Split the audio into smaller chunks for easier processing
chunk_length = 30 * 1000  # 30 seconds
chunks = [audio[i:i + chunk_length] for i in range(0, len(audio), chunk_length)]

# Temporary storage for audio chunks and transcriptions
chunk_paths = [f'temp_chunk_{i}.wav' for i in range(len(chunks))]

# Save each chunk as a temporary file
for i, chunk in enumerate(chunks):
    chunk.export(chunk_paths[i], format="wav")
```

---

### Transcription Services
Two transcription services are used in this notebook, creating two versions of the transcript, which can be compared for accuracy. 

1. **Google ASR**: A popular speech recognition service by Google.
2. **Whisper ASR**: An automatic speech recognition system by OpenAI.

"Whisper has five models (refer to the below table). Below is the table available on OpenAI’s GitHub page. According to OpenAI, four models for English-only applications, which is denoted as .en. The model performs better for tiny.en and base.en, however, differences would become less significant for the small.en and medium.en models." - [reference: Towards Data Science](https://towardsdatascience.com/speech-to-text-with-openais-whisper-53d5cea9005e)

![image](https://github.com/gilbert-umuzi/audio_transcription/assets/68329360/9e49bb66-ca57-46a0-949e-703d0193c8a6)
Ref: OpenAI’s GitHHub Page

When tested with an 8 minute .m4a audio clip of a Nigerian English speaker:
* base.en downloads and runs quickly on Google Colab, outperforming the Google ASR transcription in accuracy, but still with many errors
* medium.en is much slower to run, however its results seem to be significantly more accurate than base.en and ASR

---

### Transcription
The notebook transcribes the audio chunks using both Google ASR and Whisper ASR. The transcriptions are stored and combined to form the complete text.

---

### Output
The transcriptions from both services are combined and saved into a `.txt` file for comparison and further analysis.

```python
# Prepare the combined transcript
combined_transcription = f"Google ASR Transcript:\n{google_transcription_text}\n\nWhisper ASR Transcript:\n{whisper_transcription_text}"

# Define the path to save the transcript file
transcript_file_path = 'Transcribed_Audio.txt'

# Write the combined transcript to a .txt file
with open(transcript_file_path, 'w') as f:
    f.write(combined_transcription)

# Download the file
files.download(transcript_file_path)

```

---
