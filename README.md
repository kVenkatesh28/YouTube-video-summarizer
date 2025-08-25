# YouTube-video-summarizer
Description:
This project is a Python desktop application that takes a YouTube video link, fetches the transcript (if available), and generates a short summary of the content. It is useful for quickly understanding long videos without going through the entire transcript.
Features
Fetches video transcripts using the youtube-transcript-api.
Summarizes the transcript using a pre-trained model.
Handles long transcripts by breaking them into chunks.
Simple Tkinter-based interface to enter the link and view results.
Requirements:
Python 3
tkinter
youtube-transcript-api
transformers
torch

You can install the required packages with:
pip install youtube-transcript-api transformers torch
tkinter usually comes with Python. If not, install it separately.
How to Run
Clone the repository:
git clone https://github.com/kVenkatesh28/YouTube-video-summarizer.git
cd YouTube-video-summarizer

Run the program:
python youtube_summarizer.py
Enter the YouTube video URL in the input box and click Summarize.
The summarized text will be displayed in the result area.
Notes
1)Only works for videos that have subtitles available.
2)The quality of summaries depends on the transcript and the model.
