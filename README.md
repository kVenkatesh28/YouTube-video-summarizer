import tkinter as tk
from tkinter import scrolledtext
from youtube_transcript_api import YouTubeTranscriptApi
from youtube_transcript_api._errors import NoTranscriptFound
from transformers import pipeline
import re

# Function to get the video ID from URL
def get_video_id(url):
    pattern = r'(?:v=|\/)([0-9A-Za-z_-]{11}).*'
    match = re.search(pattern, url)
    if match:
        return match.group(1)
    else:
        return None

# Function to get the summary
def get_summary():
    url = url_entry.get()
    video_id = get_video_id(url)

    if video_id is None:
        result_text.config(state=tk.NORMAL)
        result_text.delete(1.0, tk.END)
        result_text.insert(tk.INSERT, "Invalid YouTube URL.")
        result_text.config(state=tk.DISABLED)
        return

    try:
        transcript = YouTubeTranscriptApi.get_transcript(video_id)
        full_text = " ".join([entry['text'] for entry in transcript])

        # Chunking the transcript if it's too long
        max_chunk_size = 1024
        chunks = [full_text[i:i+max_chunk_size] for i in range(0, len(full_text), max_chunk_size)]

        summary = ""
        for chunk in chunks:
            summarized_chunk = summarizer(chunk, max_length=150, min_length=30, do_sample=False)[0]['summary_text']
            summary += summarized_chunk + " "

        result_text.config(state=tk.NORMAL)
        result_text.delete(1.0, tk.END)
        result_text.insert(tk.INSERT, summary.strip())
        result_text.config(state=tk.DISABLED)
    except NoTranscriptFound:
        result_text.config(state=tk.NORMAL)
        result_text.delete(1.0, tk.END)
        result_text.insert(tk.INSERT, "Could not retrieve a transcript for this video. Subtitles may be disabled.")
        result_text.config(state=tk.DISABLED)
    except Exception as exc:
        result_text.config(state=tk.NORMAL)
        result_text.delete(1.0, tk.END)
        result_text.insert(tk.INSERT, "Error: " + str(exc))
        result_text.config(state=tk.DISABLED)

# Attempt to initialize the summarizer
try:
    summarizer = pipeline("summarization", model="sshleifer/distilbart-cnn-12-6", revision="a4f8f3e")
except Exception as exc:
    print(f"Error initializing the summarizer pipeline: {exc}")
    summarizer = None

# Create the main window
root = tk.Tk()
root.title("YouTube Transcript Summarizer")

# Apply some styling
root.geometry("600x500")
root.configure(bg="#f0f0f0")

tk.Label(root, text="YouTube URL:", bg="#f0f0f0", font=("Helvetica", 14)).pack(pady=10)
url_entry = tk.Entry(root, width=50, font=("Helvetica", 12))
url_entry.pack(pady=5)

summarize_button = tk.Button(root, text="Summarize", command=get_summary, bg="#4CAF50", fg="white", font=("Helvetica", 12))
summarize_button.pack(pady=10)

result_text = scrolledtext.ScrolledText(root, wrap=tk.WORD, width=60, height=20, state=tk.DISABLED, font=("Helvetica", 12))
result_text.pack(pady=10)

# Start the application
root.mainloop()
