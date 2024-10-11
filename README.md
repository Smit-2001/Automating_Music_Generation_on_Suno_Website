First of all, created base url API and used base url for automating music generation and download this generated music. 


import time
import requests

base_url = 'http://localhost:3000'

def custom_generate_audio(payload):
    url = f"{base_url}/api/custom_generate"
    response = requests.post(url, json=payload, headers={'Content-Type': 'application/json'})
    return response.json()

def extend_audio(payload):
    url = f"{base_url}/api/extend_audio"
    response = requests.post(url, json=payload, headers={'Content-Type': 'application/json'})
    return response.json()

def generate_audio_by_prompt(payload):
    url = f"{base_url}/api/generate"
    response = requests.post(url, json=payload, headers={'Content-Type': 'application/json'})
    return response.json()

def get_audio_information(audio_ids):
    url = f"{base_url}/api/get?ids={audio_ids}"
    response = requests.get(url)
    return response.json()

def get_quota_information():
    url = f"{base_url}/api/get_limit"
    response = requests.get(url)
    return response.json()

def get_clip(clip_id):
    url = f"{base_url}/api/clip?id={clip_id}"
    response = requests.get(url)
    return response.json()

def generate_whole_song(clip_id):
    payload = {"clip_id": clip_id}
    url = f"{base_url}/api/concat"
    response = requests.post(url, json=payload)
    return response.json()

def download_audio(audio_url, file_name):
    response = requests.get(audio_url, stream=True)
    if response.status_code == 200:
        with open(file_name, 'wb') as file:
            for chunk in response.iter_content(chunk_size=1024):
                if chunk:
                    file.write(chunk)
        print(f"Downloaded {file_name}")
    else:
        print(f"Failed to download audio from {audio_url}")

if __name__ == '__main__':
    data = generate_audio_by_prompt({
        "prompt": "An experimental drum and bass song about when you're not around",
        "make_instrumental": False,
        "wait_audio": False
    })

    ids = f"{data[0]['id']},{data[1]['id']}"
    print(f"ids: {ids}")

    for _ in range(60):
        data = get_audio_information(ids)
        if data[0]["status"] == 'streaming':
            audio_url_1 = data[0]['audio_url']
            audio_url_2 = data[1]['audio_url']
            print(f"{data[0]['id']} ==> {audio_url_1}")
            print(f"{data[1]['id']} ==> {audio_url_2}")

            # Download the generated audio files
            download_audio(audio_url_1, f"song_part_1_{data[0]['id']}.mp3")
            download_audio(audio_url_2, f"song_part_2_{data[1]['id']}.mp3")
            break
        # sleep 5s
        time.sleep(5)




https://github.com/user-attachments/assets/e2049336-5bbc-4c94-b93c-abb88fb420d0

