# Bash commands
```bash
# Download a file
wget <url> 

# View contents
cat <file_path> #Shows complete contents
nl <file_path> #Shows contents with line numbers
less <file_path> #Shows contents a page at a time
head <file_path> #Shows first 10 lines
tail <file_path> #Shows last 10 lines

# Make a file executable
chmod +x <file_path>

# Move files
mv <src_path> <destination_path> # Can be used to rename files too

# Copy files
cp <src_path> <destination_path>

# View image metadata
exiftool <file_path>
identify <file_path> # ImageMagick
```