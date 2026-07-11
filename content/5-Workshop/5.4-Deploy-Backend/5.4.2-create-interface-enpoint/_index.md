---
title : "Test VieNeu-TTS"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

VieNeu-TTS is a **locally** running model that requires no API key, but needs to download the model weights from Hugging Face on the first run. This step helps you verify that the model download and speech synthesis are working correctly before testing through the full API.

#### Step 1 — Start the Backend and Call the Voice List Endpoint

```
uvicorn main:app --reload --port 8000
```

In another terminal:
```
curl http://localhost:8000/api/v1/voices
```

The **first** call will take longer (anywhere from several seconds to a few minutes depending on network speed) because the model is being downloaded — monitor the log in the terminal running `uvicorn` to see the progress. Subsequent calls will be faster because the model is already cached in the backend process memory.

The returned result should look like this:
```json
{
  "success": true,
  "voices": [
    {"voice_id": "Xuân Vĩnh", "name": "Xuân Vĩnh", "description": "Male · Northern · Natural style", ...},
    ...
  ]
}
```
![voice list](/images/16.png)

#### Step 2 — Generate a Sample Voice Preview

```
curl http://localhost:8000/api/v1/voices/Xuân%20Vĩnh/preview
```

This endpoint is **free and requires no login**. It generates a short sample sentence and caches the file, so subsequent calls for the same voice will return immediately.

![generate voice preview](/images/15.png)

#### Verification Checklist
- [ ] `GET /api/v1/voices` returns all 10 voices
- [ ] `GET /api/v1/voices/{voice_id}/preview` returns an `audio_url` and the Vietnamese content is audible and correct
- [ ] No model loading errors appear in the `uvicorn` terminal log