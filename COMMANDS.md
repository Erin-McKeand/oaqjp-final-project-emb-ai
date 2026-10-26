# Emotion Detection project: commands per task

Run everything inside the Skills Network Theia Lab terminal.

## Setup
```
python3 -m pip install requests flask pylint
```

## Task 1: Fork and clone
```
mkdir final_project
git clone https://github.com/Erin-McKeand/Emotion-Ident final_project
cd final_project
```
Edit `README.md` so it contains the exact text `Final project`, commit and push:
```
git add README.md
git commit -m "Add project name"
git push
```
Make sure the repo is Public. Task 1 submission URL (assuming the default branch is `main`):
https://github.com/Erin-McKeand/Emotion-Ident/blob/main/README.md

Push everything as you go (the lab is not persistent):
```
git add .
git commit -m "Update emotion detection project"
git push
```

## Task 2: Import and test (save output as 2b_application_creation)
Copy `EmotionDetection/emotion_detection.py` into the repo, then:
```
python3
>>> from EmotionDetection.emotion_detection import emotion_detector
>>> emotion_detector("I love this new technology.")
```
(At this stage the task asks for the raw response text. See note below.)

## Task 3: Formatted output (save as 3b_formatted_output_test)
```
python3
>>> from EmotionDetection.emotion_detection import emotion_detector
>>> emotion_detector("I am so happy I am doing this.")
```
Expect `dominant_emotion` = joy.

## Task 4: Package (save as 4b_packaging_test)
`EmotionDetection/__init__.py` is already provided.
```
python3
>>> from EmotionDetection import emotion_detector
>>> emotion_detector("I hate working long hours.")
```
Expect `dominant_emotion` = anger. Push `__init__.py` and submit its GitHub URL (assuming `main`):
https://github.com/Erin-McKeand/Emotion-Ident/blob/main/EmotionDetection/__init__.py

## Task 5: Unit tests (save as 5b_unit_testing_result)
```
python3 test_emotion_detection.py
```

## Task 6: Flask (screenshot 6b_deployment_test.png)
```
python3 server.py
```
Open the app on port 5000 and test "I think I am having fun".

## Task 7: Error handling (screenshot 7c_error_handling_interface.png)
Already built into `emotion_detection.py` and `server.py`. Run the server and submit a blank entry. Expect: `Invalid text! Please try again!`

## Task 8: Pylint (save as 8b_static_code_analysis)
```
pylint server.py
```
Target: 10.00/10.

## Note on staged code
Tasks 2 and 3 are staged: the task 2 version returns `response.text`, the task 3 version returns the dict. The files here are the final version (Tasks 3 and 7 merged), so snapshot the earlier versions below if you need code for 2a/3a.

### Task 2a version (emotion_detection.py)
```python
import requests


def emotion_detector(text_to_analyze):
    url = ('https://sn-watson-emotion.labs.skills.network/v1/'
           'watson.runtime.nlp.v1/NlpService/EmotionPredict')
    headers = {"grpc-metadata-mm-model-id":
               "emotion_aggregated-workflow_lang_en_stock"}
    myobj = {"raw_document": {"text": text_to_analyze}}
    response = requests.post(url, json=myobj, headers=headers, timeout=15)
    return response.text
```

### Task 3a version (adds formatting and dominant emotion)
```python
import json
import requests


def emotion_detector(text_to_analyze):
    url = ('https://sn-watson-emotion.labs.skills.network/v1/'
           'watson.runtime.nlp.v1/NlpService/EmotionPredict')
    headers = {"grpc-metadata-mm-model-id":
               "emotion_aggregated-workflow_lang_en_stock"}
    myobj = {"raw_document": {"text": text_to_analyze}}
    response = requests.post(url, json=myobj, headers=headers, timeout=15)
    formatted = json.loads(response.text)
    scores = formatted["emotionPredictions"][0]["emotion"]
    result = {k: scores[k] for k in
              ("anger", "disgust", "fear", "joy", "sadness")}
    result["dominant_emotion"] = max(result, key=result.get)
    return result
```
