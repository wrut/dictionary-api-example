import os

# Check if the output file already exists
if not os.path.exists("output.txt"):
    # Create the file if it does not exist
    open("output.txt", "w").close()

# Open the input and output files
with open("input.txt", "r") as input_file, open("output.txt", "w") as output_file:
    # Loop through the lines in the input file
    for line in input_file:
        # Split the line into columns using a tab character as the delimiter
        # and remove the first and third columns
        columns = line.split("\t")[1:2] + line.split("\t")[3:]

        # Join the columns back into a string using a tab character as the delimiter
        # and write it to the output file
        output_file.write("\t".join(columns) + "\n")
