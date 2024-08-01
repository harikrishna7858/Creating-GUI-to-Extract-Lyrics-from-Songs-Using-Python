pip install requests

import tkinter as tk
from tkinter import scrolledtext
import requests
import json

# Function to fetch lyrics from Genius API
def get_lyrics(song_title, artist_name):
    api_key = 'YOUR_GENIUS_API_KEY'
    base_url = 'https://api.genius.com'
    search_url = f'{base_url}/search'
    headers = {'Authorization': f'Bearer {api_key}'}
    params = {'q': f'{song_title} {artist_name}'}

    response = requests.get(search_url, headers=headers, params=params)
    search_results = response.json()
    
    if search_results['response']['hits']:
        song_path = search_results['response']['hits'][0]['result']['path']
        song_url = f'{base_url}{song_path}'
        page_response = requests.get(song_url)
        page_html = page_response.text
        
        # Simple extraction method (you might need to use BeautifulSoup for more complex cases)
        start_idx = page_html.find('<div class="lyrics">') + len('<div class="lyrics">')
        end_idx = page_html.find('</div>', start_idx)
        lyrics = page_html[start_idx:end_idx].strip()
        return lyrics
    
    return 'Lyrics not found'

# Function to handle button click
def on_search():
    song_title = entry_song.get()
    artist_name = entry_artist.get()
    lyrics = get_lyrics(song_title, artist_name)
    text_area.delete(1.0, tk.END)
    text_area.insert(tk.END, lyrics)

# Create the main window
root = tk.Tk()
root.title('Lyrics Finder')

# Create and place widgets
tk.Label(root, text='Song Title').grid(row=0, column=0)
entry_song = tk.Entry(root)
entry_song.grid(row=0, column=1)

tk.Label(root, text='Artist Name').grid(row=1, column=0)
entry_artist = tk.Entry(root)
entry_artist.grid(row=1, column=1)

search_button = tk.Button(root, text='Search Lyrics', command=on_search)
search_button.grid(row=2, columnspan=2)

text_area = scrolledtext.ScrolledText(root, wrap=tk.WORD, width=60, height=20)
text_area.grid(row=3, columnspan=2)

# Start the GUI event loop
root.mainloop()


