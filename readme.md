# Gomichan API Tool
tool for Gomichan API data entry (not an end user facing interface)

- for use with [Gomichan-REST-Backend](https://github.com/Fonzki/Gomichan-REST-Backend)
- built with Vue 3 

## How to Run
1. get vue CLI via npm "npm install -g @vue/cli"
2. vue create gomichan
3. select Vue 3 with Vue Router and Vuex
4. clone repo into src folder
5. do 'npm run serve'
6. app is not responsive, view on laptop/desktop at url in console

## Points of Interest
- makes use of YT Embedded API to control playback of video player and read info into app.
- communicates with REST API backend with (async + await)

## Operation
### the empty tool
![blank_tool](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/blank_tool.png)

### video controls 
1. enter Youtube video ID (11-char code from URL)
2. click load and the video and label will update
3. seek to around the sentence to subtitle
4. hit mark next to start to set time in seconds
5. hit mark next to duration to set sentence length in seconds
6. use (test timing) to fine tune timing while the video loops
7. info in parentheses will be sent to the backend when request is made.

![video_controls](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/video_control.png)

### line entry and translation
1. transcribe the video clip in the "Enter Phrases" box (this is done by hand)
2. delimit phrases by grammar role with periods(.) (ex. subject/verb/object/other)
3. enter translation (also done by hand to avoid common EN->JP translation errors)

![rawline_translation](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/better_raw.png)
