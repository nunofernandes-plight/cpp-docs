---
description: "Learn more about: _fdopen, _wfdopen"
title: "_fdopen, _wfdopen"
ms.date: 05/18/2022
api_name: ["_fdopen", "_wfdopen", "_o__fdopen", "_o__wfdopen"]
api_location: ["msvcrt.dll", "msvcr80.dll", "msvcr90.dll", "msvcr100.dll", "msvcr100_clr0400.dll", "msvcr110.dll", "msvcr110_clr0400.dll", "msvcr120.dll", "msvcr120_clr0400.dll", "ucrtbase.dll", "api-ms-win-crt-stdio-l1-1-0.dll", "api-ms-win-crt-math-l1-1-0.dll", "api-ms-win-crt-private-l1-1-0.dll"]
api_type: ["DLLExport"]
topic_type: ["apiref"]
f1_keywords: ["STDIO/_fdopen", "CORECRT_WSTDIO/_wfdopen", "TCHAR/_tfdopen", "_fdopen", "_wfdopen", "_tfdopen", "wfdopen", "tfdopen"]
helpviewer_keywords: ["wfdopen function", "_fdopen function", "_wfdopen function", "tfdopen function", "fdopen function", "_tfdopen function", "streams, associating with files"]
ms.assetid: 262757ff-1e09-4472-a5b6-4325fc28f971
---
# `_fdopen`, `_wfdopen`

Associates a stream with a file that was previously opened for low-level I/O.

## Syntax

```C
FILE *_fdopen(
   int fd,
   const char *mode
);
FILE *_wfdopen(
   int fd,
   const wchar_t *mode
);
```

### Parameters

*`fd`*\
File descriptor of the open file.

*`mode`*\
Type of file access.

## Return value

Each of these functions returns a pointer to the open stream. A null pointer value indicates an error. When an error occurs, the invalid parameter handler is invoked, as described in [Parameter validation](../../c-runtime-library/parameter-validation.md). If execution is allowed to continue, `errno` is set either to `EBADF`, which indicates a bad file descriptor, or `EINVAL`, which indicates that *`mode`* was a null pointer.

For more information about these and other error codes, see [`_doserrno`, `errno`, `_sys_errlist`, and `_sys_nerr`](../../c-runtime-library/errno-doserrno-sys-errlist-and-sys-nerr.md).

## Remarks

The **`_fdopen`** function associates an I/O stream with the file that is identified by *`fd`*, and thus allows a file that is opened for low-level I/O to be buffered and formatted. **`_wfdopen`** is a wide-character version of **`_fdopen`**; the *mode* argument to **`_wfdopen`** is a wide-character string. **`_wfdopen`** and **`_fdopen`** otherwise behave identically.

File descriptors passed into **`_fdopen`** are owned by the returned `FILE *` stream. If **`_fdopen`** is successful, don't call [`_close`](close.md) on the file descriptor. Calling [`fclose`](fclose-fcloseall.md) on the returned `FILE *` also closes the file descriptor.

By default, this function's global state is scoped to the application. To change it, see [Global state in the CRT](../global-state.md).

The *`mode`* character string specifies the type of file access requested for the file:

| *`mode`* | Access |
|--------|--------|
| **`"r"`** | Opens for reading. If the file doesn't exist or can't be found, the **`fopen`** call fails. |
| **`"w"`** | Opens an empty file for writing. If the given file exists, its contents are destroyed. |
| **`"a"`** | Opens for writing at the end of the file (appending). Creates the file if it doesn't exist. |
| **`"r+"`** | Opens for both reading and writing. The file must exist. |
| **`"w+"`** | Opens an empty file for both reading and writing. If the file exists, its contents are destroyed. |
| **`"a+"`** | Opens for reading and appending. Creates the file if it doesn't exist. |

When a file is opened with the **`"a"`** or **`"a+"`** access type, all write operations occur at the end of the file. The file pointer can be repositioned by using [`fseek`](fseek-fseeki64.md) or [`rewind`](rewind.md), but it's always moved back to the end of the file before any write operation is carried out. Thus, existing data can't be overwritten. When the **`"r+"`**, **`"w+"`**, or **`"a+"`** access type is specified, both reading and writing are allowed (the file is said to be open for "update"). However, when you switch between reading and writing, there must be an intervening [`fflush`](fflush.md), [`fsetpos`](fsetpos.md), [`fseek`](fseek-fseeki64.md), or [`rewind`](rewind.md) operation. You can specify the current position for the [`fsetpos`](fsetpos.md) or [`fseek`](fseek-fseeki64.md) operation, if you want to.

In addition to the above values, the following characters can also be included in *mode* to specify the translation mode for newline characters:

| *`mode`* modifier | Behavior |
|-----------------|----------|
| **`t`** | Open in text (translated) mode. In this mode, carriage return-line feed (CR-LF) combinations are translated into one-line feeds (LF) on input, and LF characters are translated to CR-LF combinations on output. Also, Ctrl+Z is interpreted as an end-of-file character on input. |
| **`b`** | Open in binary (untranslated) mode. Any translations from **`t`** mode are suppressed. |
| **`c`** | Enable the commit flag for the associated *filename* so that the contents of the file buffer are written directly to disk if either **`fflush`** or **`_flushall`** is called. |
| **`n`** | Reset the commit flag for the associated *filename* to "no-commit." This flag is the default. It also overrides the global commit flag if you link your program with *`Commode.obj`*. The global commit flag default is "no-commit" unless you explicitly link your program with *`Commode.obj`*. |

The **`t`**, **`c`**, and **`n`** *`mode`* options are Microsoft extensions for **`fopen`** and **`_fdopen`**. Don't use them if you want to preserve ANSI portability.

If **`t`** or **`b`** isn't given in *`mode`*, the default translation mode is defined by the global variable [`_fmode`](../../c-runtime-library/fmode.md). If **`t`** or **`b`** is prefixed to the argument, the function fails and returns `NULL`. For a discussion of text and binary modes, see [Text and binary mode file I/O](../../c-runtime-library/text-and-binary-mode-file-i-o.md).

Valid characters for the *`mode`* string used in **`fopen`** and **`_fdopen`** correspond to *`oflag`* arguments used in [`_open`](open-wopen.md) and [`_sopen`](sopen-wsopen.md), as shown in this table:

| Characters in *`mode`* string | Equivalent *`oflag`* value for **`_open`** and **`_sopen`** |
|--|--|
| **`a`** | `_O_WRONLY | _O_APPEND` (usually `_O_WRONLY | _O_CREAT | _O_APPEND`) |
| **`a+`** | `_O_RDWR | _O_APPEND` (usually `_O_RDWR | _O_APPEND | _O_CREAT`) |
| **`r`** | `_O_RDONLY` |
| **`r+`** | `_O_RDWR` |
| **`w`** | `_O_WRONLY` (usually `_O_WRONLY | _O_CREAT | _O_TRUNC`) |
| **`w+`** | `_O_RDWR` (usually `_O_RDWR | _O_CREAT | _O_TRUNC`) |
| **`b`** | `_O_BINARY` |
| **`t`** | `_O_TEXT` |
| **`c`** | None |
| **`n`** | None |

## Requirements

| Function | Required header | C++ header |
|--|--|--|
| **`_fdopen`** | `<stdio.h>` | `<cstdio>` |
| **`_wfdopen`** | `<stdio.h>` or `<wchar.h>` | `<cstdio>` |

For more information on standards conformance and naming conventions in the C runtime library, see [Compatibility](../../c-runtime-library/compatibility.md).

### Generic-text routine mappings

| `<tchar.h>` routine | `_UNICODE` and `_MBCS` not defined | `_MBCS` defined | `_UNICODE` defined |
|--|--|--|--|
| **`_tfdopen`** | **`_fdopen`** | **`_fdopen`** | **`_wfdopen`** |

## Example

```c
// crt_fdopen.c
// This program opens a file by using low-level
// I/O, then uses _fdopen to switch to stream
// access. It counts the lines in the file.

#include <stdlib.h>
#include <stdio.h>
#include <fcntl.h>
#include <io.h>
#include <share.h>

int main( void )
{
   FILE *stream;
   int  fd, count = 0;
   char inbuf[128];

   // Open a file.
   if( _sopen_s( &fd, "crt_fdopen.txt", _O_RDONLY, _SH_DENYNO, 0 ) )
      exit( 1 );

   // Get stream from file descriptor.
   if( (stream = _fdopen( fd, "r" )) == NULL )
      exit( 1 );

   while( fgets( inbuf, 128, stream ) != NULL )
      count++;

   // After _fdopen, close by using fclose, not _close.
   fclose( stream );
   printf( "Lines in file: %d\n", count );
}
```

### Input: crt_fdopen.txt

```Input
Line one
Line two
```

### Output

```Output
Lines in file: 2
```

## See also

[Stream I/O](../../c-runtime-library/stream-i-o.md)\
[`_dup`, `_dup2`](dup-dup2.md)\
[`fclose`, `_fcloseall`](fclose-fcloseall.md)\
[`fopen`, `_wfopen`](fopen-wfopen.md)\
[`freopen`, `_wfreopen`](freopen-wfreopen.md)\
[`_open`, `_wopen`](open-wopen.md)
