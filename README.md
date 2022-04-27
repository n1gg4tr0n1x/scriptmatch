# scriptmatch
`scriptmatch` is designed to help pair funscripts with their corresponding videos, even if their filenames don't quite match.

## Installation

1. Create a python virtual environment (not required, but highly recommended)
2. Clone the repo
3. Install the dependencies with `pip install -r requirements.txt`

`scriptmatch` should be compatible with any reasonably recent version of python3, but the *latest* version of python3 should be used, if possible.

## General Usage
Provide one or more paths to source videos and scripts that you would like to pair, followed by a destination path to put the paired videos and scripts:

```bash
python scriptmatch.py path_to_sources [path_to_sources ...] path_to_destination
```

The `paths_to_sources` provided may be specific video or script files, or folders containing videos and scripts.  Folders will be recursively searched for both video and script files.  `path_to_destination` must be a folder on the same drive as the sources, and must already exist.

**Example**: You have a downloads folder at `D:\MyUnsortedVideosAndScripts\` that contains many unsorted files.  You woud like matches placed in `D:\Sorted\`.
```bash
python scriptmatch.py D:\MyUnsortedVideosAndScripts\ D:\Sorted\
```

**Example**: You have multiple locations that contain videos and scripts.  You would like matches placed in `D:\Sorted\`.
```bash
python scriptmatch.py D:\MySourceVideos1 D:\MySourceScripts1 D:\MoreVideosAndScripts D:\Sorted\
```

**Example**: You have a particular video file that you would like to match to any of your scripts.  If matched, you would liek them placed in `D:\Sorted\`
```bash
python scriptmatch.py D:\SomePlace\my_cool_video1.mp4 D:\MyScripts D:\Sorted\
```

## A Little More Detail
### File Types
Video files are expected to have one of the following filename extensions:
- `.mp4`
- `.mkv`
- `.wmv`

Scripts are expected to have one of the following filename extensions:
- `.funscript`

Additional filename extensions may be added via the script.

### The Matching Process
For each video file found at the given source paths:
1. If the video also exists at the destination path, it will be skipped.
2. *Exact matches* are discovered.  An exact match is a case where both the script filename is exactly the same as the video, except for the file extensions.
3. *Fuzzy matches* are discovered.  A fuzzy match uses the [`fuzzywuzzy`](https://pypi.org/project/fuzzywuzzy/) library to identify any script that has a *similar, but not exact* filename, except for the file extension.  The threshold for considering a script filename to be "similar enough" to be considered a potential match can be adjusted in the script.
4. If *exact matches* or *fuzzy matches* are found, the user will be prompted to confirm that a potential match is, indeed, a match.
5. If the user confirms a match, the video and the script will be hardlinked to the destination folder (see: Matched Pairs below).

### Matched Pairs
Once a matching video and script pair have been identified, the video and script will both be *hardlinked* to the given destination folder.  The hardlinked script will be renamed to match the video's base filename (but using the `.funscript` filename extension).

**Example**: The following source files were identified as a match:

- `D:\MySourceVideos\cool_vid_01.mp4`
- `D:\MySourceVideos\scriptsIfound\cool-vid01-script.funscript`

These will be hardlinked to:

- `D:\Destination\cool_vid_01.mp4`
- `D:\Destination\cool_vid_01.funscript`

*Note:* Because hardlinking is used, the destination folder must exist on the same volume as the source files.
*Note:* Because hardlinking is used, the matched pairs will not occupy *additional* space on the volume.
