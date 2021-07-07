# Gomichan API Tool
tool for Gomichan API data entry (not an end user facing interface)

- for use with [Gomichan-REST-Backend](https://github.com/Fonzki/Gomichan-REST-Backend)
- built with Vue 3 

## How to Run
1. get vue CLI via npm "npm install -g @vue/cli"
2. vue create gomichan
3. select Vue 3 with Vue Router and Vuex (does not use either but I wanted the option for later)
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
5. hit mark next to duration at the end of the sentence to set duration in seconds
6. use (test timing) to fine tune timing while the video loops
7. info in parentheses will be sent to the backend when request is made.

![video_controls](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/video_control.png)

### line entry and translation
1. transcribe the video clip in the "Enter Phrases" box (this is done by hand)
2. delimit phrases by grammar role with periods(.) (ex. subject/verb/object/other)
3. enter translation (translated by hand to avoid common EN->JP translation errors)

![rawline_translation](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/better_raw.png)

### defining syllables and phonemes
1. all words not in backend database appear with syllable boxes
2. enter the number of syllables, the number of boxes will update.
3. enter the 0-index of the stressed syllable (1 syllable words can't be stressed and are locked to -1)
4. enter phoneme shorthands to automatically add phoneme to label
- (Ex. ow = aʊ, schwa[x] = ə, aw = ɒ) since this is not an end user tool I have these names memorized from phoneme.json
- ambiguous phonemes can be added with SPACE (ex. ee => eer/ee OR ai => air/ai)
- misentries can be deleted with BACKSPACE when the box is empty 
(this does cause unintentional deletions but will be addressed later in development)
5. start entry with '/' in order to spell out syllables for the TTS hit SPACE to confirm and clear
6. defined words appear in green
7. repeated words that are not yet defined appear in yellow 

![defined_repeat](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/defined_repeat.png)
![predef_repeat](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/predef_repeat.png)

### defining phrases and assigning roles
1. each period delimited phrase is listed
2. translate the phrase 
(try to match up with the full translation but sometimes this is hard to do)
3. assign the grammar role the phrase plays in the sentence
4. make request

![phrase_list](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/phrase_list.png)

### line preview
1. the backend returns a JSON response with our generated line 
2. the frontend renders it in a rough preview form
3. each phrase becomes a button that toggles an info box 
4. if the TTS files were uploaded syllables would play back each syllable
5. each phoneme symbol is linked to a japanese pronunciation explanation

![line_view](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/line_view.png)
![role_preview](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/role_preview.png)

### database definition + word recognition
1. all words in the line are now green with no input syllable box.

![word_recog_update](https://github.com/Fonzki/Gomichan-API-Tool/blob/master/screenshots/word_recog_update.png)
