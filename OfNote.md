### Minor things which I felt were of note but didn't fit warrant their own file.

- Boolean values are `TRUE` and `FALSE`
- There does not seem to be any enums. Use `#defines`.
- You 'should' use U64/I64 instead as operations on smaller sizes are just expanded to that. Though, personally, I use the lower sizes for clarities sake.
- A [comment](https://github.com/minexew/TempleOS/blob/1dd8859b7803355f41d75222d01ed42d5dda057f/Kernel/KernelA.HH#L43) in `KernalA.HH` says `//(Int to F64 conversion is signed)`. Haven't tested to see if that means unsigned integers will become negative (if above their respective signed max) or what. But I found it of note.
- You can forward declare (that's the right term, right?) via `extern class classname;` even if you're declaring it in that file.
- To stop your program because it needs to abort for some reason. Use `throw;` instead of `Exit();` Exit also seems to exit the terminal it was running in, making it useless because you can't see the error messages.