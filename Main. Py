import time
import requests
import firebase_admin
from firebase_admin import credentials, db

# --- CONFIGURATION ---
# Tumhari DoodStream API Key
DOODSTREAM_API_KEY = "564321rfmmezbtkw5okjlr"

# Tumhara Updated Firebase Database URL
FIREBASE_DB_URL = "https://my-streaming-8d9ae-default-rtdb.firebaseio.com/" 

# Firebase Initialize
# Yaad rakhna: 'firebase-key.json' file tumhare code ke saath honi chahiye
cred = credentials.Certificate("firebase-key.json")
firebase_admin.initialize_app(cred, {
    'databaseURL': FIREBASE_DB_URL
})

def get_doodstream_direct_link(file_code):
    """DoodStream API se naya direct link fetch karne ke liye"""
    url = f"https://doodapi.com/api/file/direct?key={DOODSTREAM_API_KEY}&file={file_code}"
    try:
        response = requests.get(url)
        data = response.json()
        if data.get('status') == 200:
            # Result ke andar 'download_url' hota hai direct streaming ke liye
            return data.get('result').get('download_url')
    except Exception as e:
        print(f"Error fetching DoodStream link: {e}")
    return None

def update_links():
    """Firebase se purane links check karke update karne ke liye"""
    # 'movies' tumhare database ka node name hai. Agar kuch aur hai toh yahan badal dena.
    ref = db.reference('movies') 
    movies = ref.get()

    if not movies:
        print("No movies found in Firebase database.")
        return

    for movie_id, movie_data in movies.items():
        # Doodstream ka unique file code jo tumne Firebase mein store kiya hoga
        file_code = movie_data.get('dood_file_code') 
        
        if file_code:
            print(f"Checking/Updating link for: {movie_data.get('title', movie_id)}")
            new_link = get_doodstream_direct_link(file_code)
            
            if new_link:
                # Firebase mein 'stream_link' aur 'last_updated' time update ho raha hai
                ref.child(movie_id).update({
                    'stream_link': new_link,
                    'last_updated': int(time.time())
                })
                print(f"Successfully updated for {movie_id}!")
            else:
                print(f"Could not get new link for {movie_id}")

# Loop taaki script Render par chalti rahe
if __name__ == "__main__":
    print("Script started...")
    while True:
        update_links()
        print("All links checked. Waiting for next 6 hours...")
        # 6 ghante ka wait (21600 seconds)
        time.sleep(21600) 
