import json
import requests

EMOTIONS = ['anger', 'disgust', 'fear', 'joy', 'sadness']


def emotion_detector(text_to_analyze):
    url = 'https://sn-watson-emotion.labs.skills.network/v1/watson.runtime.nlp.v1/NlpService/EmotionPredict'
    headers = {"grpc-metadata-mm-model-id": "emotion_aggregated-workflow_lang_en_stock"}
    myobj = {"raw_document": {"text": text_to_analyze}}
    response = requests.post(url, json=myobj, headers=headers)

    # Blank input: Watson answers 400, so return every key with None
    if response.status_code == 400:
        result = {emotion: None for emotion in EMOTIONS}
        result['dominant_emotion'] = None
        return result

    emotions = json.loads(response.text)['emotionPredictions'][0]['emotion']
    result = {emotion: emotions[emotion] for emotion in EMOTIONS}
    result['dominant_emotion'] = max(result, key=result.get)
    return result
