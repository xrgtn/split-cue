# split-cue

Split audio CD image + cuesheet/toc to .mp3/.ogg/.flac/.wav tracks.

split-cue reads track descriptions from a .cue/.toc file and looks for
matching discs in CDDB database. It prompts user to select one disc and
whether he/she wants to edit disc/tracks descriptions.

After the prompting split-cue splits CD image to individual tracks and
encodes them to .mp3, .ogg or .flac files.

ID3 tags are created based upon the disc/tracks descriptions taken
from the source, selected by user.

### Dependencies and data formats

split-cue needs "perl5", "ffmpeg", and optionally perl CDDB library,
"lame", "oggenc" or "flac" audio encoder to operate. Basically it can
read CD images in any format that ffmpeg can understand, this means
.ape, .flac and .wav at the minimum. It can also read .clone files
produced by readom/readcd utils from cdrkit.

By default split-cue generates .mp3 files on output, but it can be
ordered to produce .ogg, .flac or .wav by "-o _format_" option.

### CDDB operation

By default split-cue queries freedb.freedb.org server. It uses perl
CDDB library by Rocco Caputo to communicate with the server. On
GNU/Debian Linux this library is provided by the "libcddb-perl"
package.

CDDB queries can be disabled by "-d" option.

### Editing disc/tracks descriptions

If user wants to edit disc/tracks descriptions, he/she should answer
"y" to the "edit?" prompt. Then he should provide information for disc
genre, year, performer, title and track titles prompts. Each track
title is processed in a special way: if it looks like "XXX / YYY", then
"XXX" is the track performer and "YYY" is its title (it's a CDDB/xmcd
"feature"). This way any track may have its own performer and this
performer may differ from the disc one. This is how CDDB records
for tribute, cover and Various Artists albums look. Because of this
"_performer_ / _title_" splitting, it's impossible to have a slash
surrounded by spaces in a track (or a performer) name, but
split-cue provides the "double slash" workaround: just double all
slashes in the title (or performer, or both) when editing track info.
For example:

    Track 12 title [Justice Tonight/Kick It Over]: Justice Tonight // Kick It Over

As you can notice in the default value for the track 12 ("Justice
Tonight/Kick It Over"), current CDDB solution for the problem is to
remove spaces around the slash, but the actual title for this Clash
song do contain spaces around slash - check [Super Black Market Clash]
(https://en.wikipedia.org/wiki/Super_Black_Market_Clash) on the
Wikipedia for a proof. With split-cue you can put " / " both into track
artist ("AD / BC") and title ("Justice Tonight / Kick It Over"):

    Track 12 title [Justice Tonight/Kick It Over]: AD // BC / Justice Tonight // Kick It Over

It's possible to avoid any prompting when you pass "-n X" option to
split-cue. The "X" number after "-n" option indicates which disc info
record to use. "-n0" stands for .cue file data. "-n1" means taking the
1st matching CDDB record. "-n2" is for the 2nd CDDB record and so on.
"-n-1" means the last matching record, "-n-2" - the one before last
etc. To take .cue data without any prompting, use "-dn0".

### Caching edited descriptions

When user edits disc info, it's cached in the ~/.cddb/_genre_/_discid_
file. When user selects a disc info that he edited before, the cached
data is retrieved so he/she doesn't need to edit the same disc/tracks
descriptions twice (or more).

Cached data retrieval may be disabled by "-c" option (but cache writes
may not).

### Encoding

split-cue creates separate subdirectory in current directory and
outputs resulting .mp3 files there. Output files would typically be
named like this:

    ./year - albumartist - album/01. artist1 - song1.mp3
    ./year - albumartist - album/02. artist2 - song2.mp3
    ...

When disc year, artist or album information isn't available in either
.cue file or on CDDB server and user didn't care to provide it by
editing the description, split-cue uses name of the given .cue file as
a basis for the output subdirectory. If a track title isn't available,
the track will be named just "_nn_.mp3".

By default, split-cue runs "lame" with "-V2" option, which produces VBR
(Variable Bit Rate) mp3 file with quality level 2 (approximately
200kbits).
* "-b R" option causes split-cue to produce CBR (Constant Bit Rate) mp3
  files with bitrate of Rkbits.
* "-q L" option produces VBR files at quality level L (0..9 with level
  0 being the highest). "-q" has precedence over "-b".

With "oggenc" "-b N" and "-q L" options are processed similarly, but
quality level may be frational like 4.5 and must fall in -1..10 range
where level 10 is the highest one (level 5 is assumed by default,
reults in approximately 190kbits rate).  With "flac" the "-q" option is
translated to --compression-level-0..8, 0 being the fastest, 8 -- the
highest and level 6 used by default. The "-b" option is completely
ignored when producing .flac's. With .wav output both "-q" and "-b" are
ignored.

### Metadata tags

For .mp files disc year, artist, album, genre, track number, title and
performer data are put into ID3v1 tag or ID3v1+ID3v2. The latter method
is only used when separate track performer is specified in track info
(typically on tribute, cover or Various Artists albums), and it uses
--TPE2 tag to store album performer separately from track one.

For .ogg and .flac files their respective standard tags are used. When
there are separate track and disc performer, the latter is put into
ALBUMARTIST tag.

For .wav files no metadata tags are created.
