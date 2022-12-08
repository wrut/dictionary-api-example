import openpyxl
import requests

# Open the text file containing the words
with open("words.txt") as file:
    # Read the words from the file and store them in a list
    word_list = file.read().splitlines()

# Create a new Excel workbook
workbook = openpyxl.Workbook()

# Get the active sheet in the workbook
sheet = workbook.active

# Loop through the words in the list
for word in word_list:
    # Use the DictionaryAPI.dev service to get the definition of the word
    response = requests.get(f"https://api.dictionaryapi.dev/api/v2/entries/en/{word}")

    # Parse the JSON data and extract the first definition and example sentence
    try:
        data = response.json()  # Move this line inside the try block

        # Check if the word was found in the dictionary
        if "title" in data and data["title"] == "No Definitions Found":
            print(f"Word '{word}' was not found in the dictionary")
            continue

        # If the word was found, extract its definition and example sentence
        if data and len(data) > 0:  # Check if the list is not empty
            meanings = data[0]["meanings"]
            for meaning in meanings:
                definition = meaning["definitions"][0]["definition"]

                # Check if the example sentence exists
                if "example" in meaning["definitions"][0]:
                    example_sentence = meaning["definitions"][0]["example"]
                elif len(meaning["definitions"]) > 1 and "example" in meaning["definitions"][1]:
                    example_sentence = meaning["definitions"][1]["example"]
                elif len(meaning["definitions"]) > 2 and "example" in meaning["definitions"][2]:
                    example_sentence = meaning["definitions"][2]["example"]
                else:
                    example_sentence = ""

                # Skip adding the word if there is no example sentence
                if example_sentence == "":
                    print(f"This word '{word}' with this definition: '{definition}' doesn't have an example sentence.")
                    continue
                # Replace the word in the example sentence with a blank space
                blank_sentence = example_sentence.lower().replace(word, "___").capitalize()

                # Add a new row to the sheet with the word, its definition, example sentence, and blank sentence
                sheet.append([word, definition, example_sentence, blank_sentence])

    except ValueError as error:
        # Print an error message if there is a problem parsing the JSON data
        print(f"Error parsing JSON data for word '{word}': {error}")
        continue

# Save the workbook to a file
workbook.save("words.xlsx")
