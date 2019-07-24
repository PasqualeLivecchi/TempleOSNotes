### Checking For File Existence

Note: I believe this also returns true for directories.
TODO: Check above note for truth, I think it's true, but make sure.
`Bool FileFind(U8* filename);`

### Checking If File Is Directory
`Bool IsDir(U8* filename);`
Returns if it is a directory.

### Reading File Into Memory
`U8* FileRead(U8* filename, I64* *_size=NULL, I64* _attr=NULL);`
Returns a pointer to an allocated buffer with the contents of the file with the name `filename`. Stores the size of this buffer in `_size`.
I don't know what `_attr` does.
TODO: Find out what attr does