# anki-cards-ai-generator
Flash cards and spaced repetition can be combined into powerful and effective tool for many areas of studying, from languages to university subjects.
The creation of these cards is an interesting, but sometimes time-consuming process.
There are many automations (e.g. [AnkiBrain](https://ankiweb.net/shared/info/1915225457)) that can create the anki cards from prompts using ChatGPT.
However, these tools do not allow prompt customization or image creation.

## Purpose
Automate the creation of the Anki cards for learning words or phrases in different languages. 
This tool is a card generator, [Anki](https://apps.ankiweb.net/) can be used for learning process.

Workflow:
1. Create list of words or phrases with some context
2. Start the generation
3. Enjoy the new Anki deck with generated text, image, audio and dictionary link in your Anki app

### Supported Languages
- English (with [Cambridge Dictionary](https://dictionary.cambridge.org/))
- German (with [DWDS](https://www.dwds.de/))

### Language Levels
You can choose a CERF language level for card generation: A1, A2, B1, B2, C1, C2.

Note, that not all words can be explained at the beginner levels. 

### Synchronization
Cards are imported into local Anki client, and pictures are copied to the Anki media folder (e.g. to 'C:\\Users\\User\\AppData\\Roaming\\Anki2\\User 1\\collection.media').  

Afterwards, Anki can be synchronized with AnkiWeb, and the deck can be used from other devices. Versions for different devices can downloaded [here](https://apps.ankiweb.net/#windows). 

The synchronization is very straightforward, and is described [here](https://docs.ankiweb.net/syncing.html).

## Prerequisites
1. This is an application that automates Anki cards creation process using the ChatGPT, DALLE and TTS models. Your [OpenAI API](https://platform.openai.com/api-keys) key is required. You can set environment variable OPENAI_API_KEY or use --openai_api_key option.
2. [Anki](https://apps.ankiweb.net/) must be installed.
3. Add-on [AnkiConnect](https://ankiweb.net/shared/info/2055492159) is used for the import of the cards. It must be installed. AnkiConnect website contains short installation guide.
4. Anki is running.
5. Tool is compatible with python 3.10+

## Usage
Syntax:  
```bash
read-generate-import.py input_file processing_directory \
          [-h] \
          [--openai_api_key OPENAI_API_KEY] \
          [--deck_name DECK_NAME] \
          [--anki_media_directory_path ANKI_MEDIA_DIRECTORY_PATH] \
          [--language {english,german}] \
          [--level {A1,A2,B1,B2,C1,C2}] 
```

input_file processing_directory and processing directory can be relative or absolute paths.

### Default settings
```bash
python -m generator.read-generate-import ./demo/input_words.csv ./processing
```
Defaults are:
- Language is English
- Level is C1
- OpenAI API key is and expected to be set in environment variable OPENAI_API_KEY
- Deck name is autogenerated, containing current date
- Default Anki media directory path for Windows, Linux and MacOS

### Custom settings
```bash
python -m generator.read-generate-import ./demo/input_words.csv ./processing  \
          --language="german" \
          --level="B2" \
          --openai_api_key="YOUR_KEY" \
          --deck_name="my_amazing_deck" \
          --anki_media_directory_path="custom_path/Anki2/User/collection.media"
```

You can adjust and use the [run_generator.cmd](run_generator.cmd) for Windows or [run_generator.bsh](run_generator.bsh) for Linux/MacOS to avoid typing the defaults and paths every time.

### Input
This tool reads a file as input.
It can be a CSV file with semicolon as separator (you can use commas in sentences and context), or an Excel file.
Header "word;context" is expected.
Example:
```csv
word;context
tax fraud;
purchasing power parity;economy
affect;
consciousness;the state of human being
free will;
```

### Output
Card materials are created in the specified directory. 
Tool creates for each word:
- json with card text, paths and links
- png with generated image  
- mp3 with generated audio
- dictionary link (if the link was found)

After generation, these files are used for card creation. 
Generated cards are imported into Anki deck automatically.

An example input file, generated elements and card screenshots can be found in [Words Demo](demo/words_and_cards/english).
Examples for different language levels can be found in [Levels Demo](demo/different_levels)

## OpenAI API
Text: [gpt-4o](https://platform.openai.com/docs/models/gpt-4o)  
Image: [dall-e-3](https://platform.openai.com/docs/guides/images/usage), 3 RPM, 200 RPD - main throughput limitation  
Audio: [tts-1-hd](https://platform.openai.com/docs/guides/text-to-speech)  

### Cost
Billing: https://platform.openai.com/settings/organization/billing/overview

DALLE-3 call is the most expensive step, 0.04$ per image. This is expensive compared with free images, but:
- Sometimes it is really difficult to find an image that describes some abstract content.
- These images boost (at least mine) learning process a lot
- Cards are much better than DALLE-2 cards in this use case

Text generation is much cheaper, less than 0.01$ per card. Total cost of a card is <= 0.05$ pro card.

## FAQ
Q: Audio file plays when I'm opening the card, I don't like it!  
A: By default, Anki automatically plays audio on the front and back of cards. You can choose ["Don't play audio automatically"](https://docs.ankiweb.net/deck-options.html) in deck options. In this case Anki will not play audio until you click the replay audio button.  
*****
Q: I don't like the generated cards!  
A: At first, try to regenerate the card, maybe provide more context. If the tool creates something totally wrong - please create an issue on GitHub. You can also adjust the prompts at your own discretion.  
*****
Q: Will this tool correct my typos?  
A: No, Garbage In - Garbage Out. If ChatGPT understands your input, the tool will create a contextually correct card that may include your typo on the back of the card. You can fix it manually in the anki app or regenerate the card.  
*****
Q: Card text is too complicated, I don't know these words or constructions!  
A: You can set the language complexity with --level option.  
*****
Q: I like the tool, but I would like to use it with another language...  
A: For this purpose, it is required to:  
1. Check whether the language is supported by ChatGPT.  
2. Translate prompts for text generation, and adjust them. Probably we will require help of person with a good language level. 
3. Good dictionary, to place a link in cards (dictionary can be ignored).  
4. Create an issue with this information, or create a fork.  
*****
Q: I'm getting an error:  
```bash
Traceback (most recent call last):
  File "<frozen runpy>", line 198, in _run_module_as_main
  File "<frozen runpy>", line 88, in _run_code
  File "D:\AnkiProject\anki-cards-ai-generator\generator\read-generate-import.py", line 9, in <module>
    from generator import generate_cards, entities
  File "D:\AnkiProject\anki-cards-ai-generator\generator\generate_cards.py", line 4, in <module>
    from generator.api_calls import openai_image, openai_text, openai_audio
  File "D:\AnkiProject\anki-cards-ai-generator\generator\api_calls\openai_image.py", line 2, in <module>
    from openai import OpenAI
ModuleNotFoundError: No module named 'openai'
```
A: Tool is supplied with [requirements.txt](requirements.txt), which should be used with venv. When requirements.txt and venv don't ring a bell, then you can install the dependencies in your normal python environment using `pip install`:  
```bash
pip install -r requirements.txt
```
*****
Q: I'm getting an error:
```bash
Traceback (most recent call last):
  File "<frozen runpy>", line 198, in _run_module_as_main
  File "<frozen runpy>", line 88, in _run_code
  File "D:\AnkiProject\anki-cards-ai-generator\generator\read-generate-import.py", line 87, in <module>
    main()
  File "D:\AnkiProject\anki-cards-ai-generator\generator\read-generate-import.py", line 72, in main
    validation.check_anki_connect()
  File "D:\AnkiProject\anki-cards-ai-generator\generator\validation.py", line 22, in check_anki_connect
    raise IOError("Failed to connect to AnkiConnect. It might be not running")
OSError: Failed to connect to AnkiConnect. It might be not running
```
A: Check whether Anki app is running and AnkiConnect is installed
*****
Q: I'm getting an error:
```bash
Traceback (most recent call last):
  File "<frozen runpy>", line 198, in _run_module_as_main
  File "<frozen runpy>", line 88, in _run_code
  File "D:\AnkiProject\anki-cards-ai-generator\generator\read-generate-import.py", line 87, in <module>
    main()
  File "D:\AnkiProject\anki-cards-ai-generator\generator\read-generate-import.py", line 75, in main
    input_words: list[WordWithContext] = read_input_file.read_file_based_on_extension(args.input_file)
                                         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\AnkiProject\anki-cards-ai-generator\generator\input\read_input_file.py", line 55, in read_file_based_on_extension
    return read_csv_file(file_path)
           ^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\AnkiProject\anki-cards-ai-generator\generator\input\read_input_file.py", line 38, in read_csv_file
    return read_from_dataframe(df)
           ^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\AnkiProject\anki-cards-ai-generator\generator\input\read_input_file.py", line 26, in read_from_dataframe
    check_columns(df)
  File "D:\AnkiProject\anki-cards-ai-generator\generator\input\read_input_file.py", line 19, in check_columns
    raise ValueError(f"Missing required columns: {', '.join(missing_cols)}")
ValueError: Missing required columns: word, context
```
A: Probably you are using a CSV file, which is not separated with semicolon (this guy `;`).

