# Learnzio
import whisper
from moviepy.editor import VideoFileClip, concatenate_videoclips
import os
import tempfile

# Load Whisper model
model = whisper.load_model("base")

def transcribe_audio(video_path):
    print("Transcribing audio...")
    return model.transcribe(video_path)

def remove_silence(video_path, transcription):
    print("Removing silence...")
    clips = []
    video = VideoFileClip(video_path)

    for segment in transcription["segments"]:
        start = segment["start"]
        end = segment["end"]
        clip = video.subclip(start, end)
        clips.append(clip)

    final_clip = concatenate_videoclips(clips)
    return final_clip

def auto_edit_video(input_path, output_path="edited_output.mp4"):
    # Step 1: Transcribe
    result = transcribe_audio(input_path)

    # Step 2: Remove silent parts
    edited = remove_silence(input_path, result)

    # Step 3: Save output
    edited.write_videofile(output_path, codec="libx264", audio_codec="aac")
    print(f"Edited video saved as {output_path}")

# Run the editor
if __name__ == "__main__":
    video_file = "input_video.mp4"  # Change to your video file path
    auto_edit_video(video_file)
