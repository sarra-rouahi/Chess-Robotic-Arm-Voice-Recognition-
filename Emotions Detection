import picovoice
import soundfile as sf
import librosa
import numpy as np

# Load Picovoice for speech recognition
def load_picovoice():
    # Initialize Picovoice (use your own Picovoice access keys here)
    access_key = "your_picovoice_access_key"  # Replace with your Picovoice access key
    picovoice_engine = picovoice.create(
        access_key=access_key,
        # Optionally, you can add keywords and wake words if needed
    )
    return picovoice_engine

# Record audio from the microphone
def record_audio(picovoice_engine):
    print("Listening for speech...")
    audio_data = picovoice_engine.listen()  # Capture audio
    # Save the audio to a file
    with open("recorded_audio.wav", "wb") as f:
        f.write(audio_data)
    print("Recording saved.")
    return "recorded_audio.wav"

# Extract audio features using librosa (pitch and energy)
def extract_audio_features(audio_file):
    # Load audio file using librosa
    y, sr = librosa.load(audio_file)
    
    # Extract pitch and energy features
    pitch, _ = librosa.core.piptrack(y=y, sr=sr)
    energy = librosa.feature.rms(y=y)
    
    # Average pitch and energy values
    avg_pitch = np.mean(pitch)
    avg_energy = np.mean(energy)
    
    return avg_pitch, avg_energy

# Emotion classification based on extracted features
def classify_emotion(pitch, energy):
    # Simple rule-based emotion classification
    if pitch > 200 and energy > 0.1:
        return "Happy"
    elif pitch < 150 and energy < 0.05:
        return "Sad"
    elif energy > 0.2:
        return "Angry"
    else:
        return "Neutral"

# Main function
def main():
    # Initialize Picovoice engine
    picovoice_engine = load_picovoice()
    
    # Record audio from the microphone
    audio_file = record_audio(picovoice_engine)
    
    # Extract features from the recorded audio
    pitch, energy = extract_audio_features(audio_file)
    
    # Classify the emotion based on the extracted features
    emotion = classify_emotion(pitch, energy)
    
    print(f"Detected Emotion: {emotion}")

if __name__ == "__main__":
    main()
