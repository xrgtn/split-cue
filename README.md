# split-ape-cue

Split audio CD .ape/.cue image to .mp3 tracks.

split-ape-cue reads track descriptions from a .cue file and looks for
matching discs in CDDB database. It prompts user to select one disc and
whether he/she wants to edit disc/tracks descriptions.

After prompting it splits the .ape file to individual tracks and
encodes them to .mp3 files.

ID3 tags are created based upon the disc/tracks descriptions taken
from the source, selected by user.

### CDDB operation

By default split-ape-cue queries freedb.freedb.org server. It uses
perl's CDDB library by Rocco Caputo to do that. CDDB queries can be
disabled by "-d" option.

### Editing disc/tracks descriptions

If user wants to edit disc/tracks descriptions, he/she should answer
"y" to the "edit?" prompt. Then he should provide information for disc
genre, year, performer, title and track titles prompt. Each track title
is processed in a special way: if it looks like "XXX / YYY", then "XXX"
is the track performer and "YYY" is its title (it's a CDDB/xmcd
"feature"). This way any track may have its own performer and this
performer may differ from the disc performer. This is how CDDB records
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

When user edits disc info, it's cached in the ~/.cddb/_genre_/_discid_
file. When user selects a disc info that he edited before, the cached
data is retrieved so he/she doesn't need to edit the same disc/tracks
descriptions twice (or more).

Cached data retrieval may be disabled by "-c" option (but cache writes
may not).
