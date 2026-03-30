# -----------------------------
# IMPORTING REQUIRED LIBRARIES
# -----------------------------

import pyttsx3                    # For text-to-speech
import speech_recognition as sr   # For speech recognition
import datetime                   # For working with date and time
import wikipedia                  # For getting information from Wikipedia
import webbrowser                 # For opening websites
import os                         # For interacting with the operating system


engine = pyttsx3.init()
voices = engine.getProperty('voices')

# voices[0] -> Male voice
# voices[1] -> Female voice

try:
    engine = pyttsx3.init('sapi5') 
except Exception as e:
    print(f"Driver error: {e}. Falling back to default.")
    engine = pyttsx3.init()

# Test the engine immediately
engine.say("Testing speakers")
engine.runAndWait()

voices = engine.getProperty('voices')
if len(voices) > 0:
    # Use index 1 (usually a female voice) if 0 is not working
    engine.setProperty('voice', voices[0].id) 

def speak(audio):
    # Print the text so you can see if the function is even being called
    print(f"JARVIS: {audio}") 
    engine.say(audio)
    engine.runAndWait()

def wishMe():
    """Greets the user based on the time of day"""

    hour = int(datetime.datetime.now().hour)

    if hour>=0 and hour<12:
        speak("Good Morning!")

    elif hour>=12 and hour<18:
        speak("Good Afternoon!")

    else:
        speak("Good Evening!")

    speak("I am your Assistant. How can I help you today?")

def takeCommand():
    """
    This function listens to the microphone and converts speech into text.

    Returns:
    string -> user's spoken command
    """

    r = sr.Recognizer()

    # Try for microphone as an innput source
    with sr.Microphone() as source:

        print("Listening...")

        # Adjusts for background noise automatically
        r.adjust_for_ambient_noise(source, duration=1)
        r.pause_threshold = 1

        audio = r.listen(source)

    try:
        print("Recognizing...")
        query = r.recognize_google(audio, language='en-in')

        print(f"User said: {query}\n")

    except Exception as e:

        print("Could not understand audio")
        return "None"

    return query

if __name__ == "__main__":
    # Ensure the engine is fully initialized
    speak("Initializing JARVIS...") 
    
    # Now call the greeting
    wishMe()

    # step 2: continouous loop
    while True:

        query = takeCommand()

        # If speech recognition fails
        if query == "None":
            continue

        query = query.lower()

        # Logic for executing tasks based on query
        if 'wikipedia' in query:

            speak('Searching Wikipedia...')

            query = query.replace("wikipedia", "")

            # Sometimes wikipedia.summary() throws errors if topic is unclear.
            try:
                results = wikipedia.summary(query, sentences=2)
                speak("According to Wikipedia")
                print(results)
                speak(results)

            except:
                speak("Sorry, I couldn't find information on that.")


        elif 'open youtube' in query:

            webbrowser.open("https://www.youtube.com")
            speak("Opening YouTube")

        elif 'open google' in query:

            webbrowser.open("https://www.google.com")
            speak("Opening Google")

        elif 'time' in query:
            strTime = datetime.datetime.now().strftime("%H:%M:%S")
            speak(f"Sir, the time is {strTime}")

        elif 'open code' in query:
            # Note: Path will vary based on student's PC
            codePath = "C:\\Users\\<USERNAME>\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe"
            os.startfile(codePath)
            speak("Opening Visual Studio Code")

        elif 'stop' in query or 'exit' in query:
            speak("Goodbye! Have a great day.")
            break

  
