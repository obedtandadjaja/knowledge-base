# Delta Encoding

Delta encoding is used to send updates to a certain object without sending the entire object after the initial connection. Instead, it sends just the difference between the updated version and the current version in the machine. This technology is used in Git and xdelta.

## Usage in Git

Git stores object in `packfiles` and `snapshots` (so that you don't have to playback every single change ever on a given file to get the current version). In addition, Git also compresses the contents of files with `zlib`.

The intial format in which Git saves objects on disk is called a "loose" object format. However, occasionally Git packs up several of these objects into a single binary file called a "packfile" in order to save space and be more efficient. It does this when there are too many loose objects around. Every push you make to remote server will do this operation.

Every packfile has an index file that contains offsets into that packfile so you can quickly seek to a specific object.

Git looks for files that are named and sized similarly, and stores just the deltas from one version of the file to the next.

The latest version of the file is the one that is stored intact, whereas the original version is stored as a delta -- this is because you're most likely to need faster access to the most recent version of the file.

Reference: https://www.git-scm.com/book/en/v2/Git-Internals-Packfiles
