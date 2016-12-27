# split-ape-cue

Split audio CD .ape/.cue image to .mp3 tracks.

split-ape-cue reads track descriptions from a .cue file and looks for
matching discs in CDDB database. It prompts user to select one disc and
whether he/she wants to edit disc/tracks descriptions.

After the prompting split-ape-cue splits the .ape file to individual
tracks and encodes them to .mp3 files.

ID3 tags are created based upon the disc/tracks descriptions taken
from the source, selected by user.

### CDDB operation

By default split-ape-cue queries freedb.freedb.org server. It uses
perl CDDB library by Rocco Caputo to communicate with the server. On
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
split-ape-cue provides the "double slash" workaround: just double all
slashes in the title (or performer, or both) when editing track info.
For example:

    Track 12 title [Justice Tonight/Kick It Over]: Justice Tonight // Kick It Over

As you can see, current CDDB solution for the problem is to remove
spaces around the slash, but the actual title for this Clash song do
contain spaces around slash - check [Super Black Market Clash]
(https://en.wikipedia.org/wiki/Super_Black_Market_Clash) description on
the Wikipedia for a proof. With split-ape-cue you can put " / " both
into artist name ("AD / BC") and track ("Justice Tonight / Kick It
Over"):

    Track 12 title [???]: AD // BC / Justice Tonight // Kick It Over

It's possible to avoid any user prompts when you pass "-n X" option to
split-ape-cue. The "X" number after "-n" option indicates which disc
info record to use. "-n0" stands for .cue file data. "-n1" means
taking the 1st matching CDDB record. "-n2" is for the 2nd CDDB record
and so on. "-n-1" means the last matching record, "-n-2" - the one
before last etc. To take .cue data without any prompting, use "-dn0".

### Caching edited descriptions

When user edits disc info, it's cached in the ~/.cddb/_genre_/_discid_
file. When user selects a disc info that he edited before, the cached
data is retrieved so he/she doesn't need to edit the same disc/tracks
descriptions twice (or more).

Cached data retrieval may be disabled by "-c" option (but cache writes
may not).

### Encoding

split-ape-cue creates separate subdirectory in current directory and
outputs resulting .mp3 files there. Output files would typically be
named like this:

    ./_year_ - _artist_ - _album_/01. _artist_ - _song1_.mp3
    ./_year_ - _artist_ - _album_/02. _artist_ - _song2_.mp3
    ...

When the disc year, artist or album information isn't available in
either .cue file or on CDDB server and user didn't care to provide it
by editing the disc description, split-ape-cue uses name of the given
.cue file as a basis for the output subdirectory. If a track title
isn't available, the track will be named just "_nn_.mp3" where _nn_ it
this track number.

By default, split-ape-cue runs "lame" with "-V2" option, which produces
VBR (Variable Bit Rate) mp3 file with quality level 2 (approximately
190kbits).
* "-b N" option causes split-ape-cue to produce CBR (Constant Bit Rate)
  mp3 files with bitrate of Nkbits.
* "-q L" option produces VBR files at quality level L (0-9).

Disc year, artist, album, genre, track number, title and performer data
are put into ID3v1 tag or ID3v1+ID3v2. The latter method is only used
when separate track performer is specified in track info (typically
on tribute, cover or Various Artists albums), and it uses --TPE2 tag to
store album performer separately from track one.
