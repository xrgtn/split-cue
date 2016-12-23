# split-ape-cue
Split audio CD .ape/.cue image to .mp3 tracks.

split-ape-cue takes as an argument a name of a .cue file, parses .cue
file contents to find start positions of audio tracks, number of audio
tracks and (if possible) track and disc titles, performer/performers
and music genre name. Then it splits corresponding .ape file to
individual tracks and encodes them to mp3 files using lame.

If required information is missing like track title for example,
split-ape-cue queries freedb.org for it.
