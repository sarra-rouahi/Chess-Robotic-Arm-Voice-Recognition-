from pvleopard import Leopard  
from gtts import gTTS         
from chess import Board       
import os                     
import serial                 
import time
import re  

# Initialize Picovoice Leopard for voice-to-text
leopard = Leopard(access_key="YOUR_PICOVOICE_ACCESS_KEY")  

# Initialize a chess board object
chess_board = Board()  

# Setup serial communication for hardware
ser = serial.Serial('/dev/ttyUSB0', 9600, timeout=1)  


def capture_voice():
    """Record speech and convert to text using Picovoice Leopard."""
    print("Listening... Speak your command (e.g., 'move knight to e5').")
    try:
        from pvrecorder import PvRecorder  # Import recorder for audio input
        recorder = PvRecorder(device_index=-1, frame_length=512)
        recorder.start()  # Start recording
        frames = [recorder.read() for _ in range(100)]  # Capture audio frames
        recorder.stop()  # Stop recording
        transcript, _ = leopard.process(frames)  # Process audio to text
        print("Recognized Command:", transcript)
        return transcript.lower() if transcript else None
    except Exception as e:
        print("Voice input error:", e)
        return None


def parse_command(command):
    """Parse and validate the chess move command."""
    try:
        if not command.startswith("move"):
            raise ValueError("Invalid format. Use 'move [piece] to [square]'.")

        # Extract piece and target square using regex
        match = re.match(r"move (\w+) to ([a-h][1-8])", command)
        if match:
            piece = match.group(1).lower()  # Extract piece
            target = match.group(2)  # Extract target square
            print(f"Parsed Command: Move {piece} to {target}")
            return piece, target
        else:
            raise ValueError("Invalid command format.")
    except Exception as e:
        print("Command parsing error:", e)
        return None, None


def validate_move(piece, target):
    """Validate if the move is legal based on chess rules."""
    try:
        legal_moves = [str(move) for move in chess_board.legal_moves]  # Get all legal moves
        move_str = f"{piece[0].lower()}{target}"  # Construct move string
        
        if move_str in legal_moves:
            chess_board.push_san(move_str)  # Apply the move on the board
            print("Move is valid!")
            return True
        else:
            print(f"Invalid move: {move_str}")
            return False
    except Exception as e:
        print("Chess validation error:", e)
        return False


def send_to_hardware(piece, target):
    """Send move command to robotic arm via serial communication."""
    try:
        command = f"{piece}:{target}"  # Format command for the hardware
        ser.write(command.encode())  # Send command
        print(f"Command sent to hardware: {command}")
    except Exception as e:
        print("Hardware communication error:", e)


def text_to_speech(response):
    """Convert text to speech for feedback."""
    try:
        tts = gTTS(response)  # Convert text to audio
        tts.save("response.mp3")  # Save audio file
        os.system("start response.mp3")  # Play audio feedback
        print("Audio feedback played.")
    except Exception as e:
        print("TTS error:", e)


def main():
    """Main loop to handle voice commands and execute chess moves."""
    while True:
        print("\nWaiting for your command...")
        command = capture_voice()  # Get voice input
        
        if not command:
            text_to_speech("Sorry, I didn't catch that. Try again.")  # Handle no input
            continue
        
        piece, target = parse_command(command)  # Parse the command
        if not piece or not target:
            text_to_speech("Invalid command format. Please try again.")  # Handle invalid format
            continue
        
        if validate_move(piece, target):  # Check if the move is valid
            send_to_hardware(piece, target)  # Send command to robotic arm
            text_to_speech(f"Moved {piece} to {target}.")  # Provide feedback
        else:
            text_to_speech("Invalid move. Please try again.")  # Handle invalid move


def enhanced_error_handling():
    """Improves overall error handling by logging all errors."""
    try:
        main()  # Run the main loop
    except KeyboardInterrupt:
        print("Operation interrupted by the user.")  # Handle user interruption
        text_to_speech("Operation interrupted.")
    except Exception as e:
        print(f"Unexpected error occurred: {e}")  # Catch all other errors
        text_to_speech("An unexpected error occurred. Please try again.")
    finally:
        print("Shutting down system.")
        ser.close()  # Close serial communication


def test_system():
    """Simulate the full system with test commands."""
    test_commands = [
        "move knight to e5",      # Valid command
        "move queen to d4",       # Valid command
        "move king to g1",        # Valid command
        "move rook to b3",        # Valid command
        "jump knight to f6"       # Invalid command
    ]
    
    for command in test_commands:
        print(f"Testing command: {command}")
        piece, target = parse_command(command)  # Parse test command
        if piece and target:
            if validate_move(piece, target):  # Validate test move
                send_to_hardware(piece, target)  # Send to hardware
                text_to_speech(f"Moved {piece} to {target}.")  # Feedback
            else:
                text_to_speech(f"Invalid move for {piece} to {target}.")  # Feedback
        else:
            text_to_speech("Invalid command format.")  # Handle invalid format
        time.sleep(2)  # Pause between tests


if __name__ == "__main__":
    enhanced_error_handling()  # Start the program

    
    
