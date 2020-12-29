# RC0 file format

A file with the file extension `.rc0`, creatable using Boss's RC-10R
Rhythm Converter software. It appears to be a file header, a parts
list, followed by what looks like MIDI, and padded with zeroes to 1960
bytes.

## File header

The initial few bytes from a whipped-together RC0 file header from a
MIDI file for the Black Keys' song "Lonely Boy" looks like this:

```shell
00000000: 5054 4e5f 3030 3030 b49c 0000 4c6f 6e65  PTN_0000....Lone
00000010: 6c79 2042 0000 0000 0000 a900 0404 0000  ly B............
00000020: 0000 0500 8200 0000 0000 0000 b305 0000  ................
00000030: 0500 0400 8200 0000 0000 0000 b305 0000  ................
00000040: 1100 0100 8200 0000 0000 0000 b305 0000  ................
00000050: 1100 0400 8200 0000 0000 0000 b305 0000  ................
00000060: 1100 0100 8200 0000 0000 0000 b305 0000  ................
00000070: 8100 0100 8200 0000 0000 0000 b305 0000  ................
```

Current hypothesis is that the first 32 bytes constitute a file
header.

### Tag
```shell
5054 4e5f 3030 3030
```

I'm calling the first 8 bytes of the file header the "tag", and it's
the ASCII string `"PTN_0000"`.

### [Unknown]
```shell
b49c
```

Two bytes of `b49c`. Possibly just a magic number/secondary tag.

### [Unknown]
```shell
0000
```
Two bytes of zeroes. Possibly unused/reserved, possibly a continuation
of the previous unknown field.

### Title
```shell
4c6f 6e65 6c79 2042
```

Eight bytes of ASCII track title, also displayed in the RC-10R's LED
panel when the track is loaded. In the example above, the string is
`"Lonely B"`. Shorter strings gets padded with zeroes.

### [Unknown]
```shell
0000 0000
```

Four bytes of zeroes. Possibly unused/reserved.

### Kit
```shell
0000
```

Two bytes of little-endian integer, `0000` through `0f00`, signifying
drum kit used:

| Hex    | Kit         |
| :----- | :---------- |
| `0000` | Studio      |
| `0100` | Live        |
| `0200` | Light       |
| `0300` | Heavy       |
| `0400` | Rock        |
| `0500` | Metal       |
| `0600` | Jazz        |
| `0700` | Brushes     |
| `0800` | Cajon       |
| `0900` | Drum&Bs     |
| `0a00` | R&D         |
| `0b00` | Dance       |
| `0c00` | Techno      |
| `0d00` | Dance Beats |
| `0e00` | Hiphop      |
| `0f00` | 808+909     |

### Tempo
```shell
a900
```

Two bytes of little-endian integer, `2800` through `5e01`, signifying
tempo in the range of (decimal) 40 to 350 BPM.

### Time
```shell
0404
```

Two bytes of time signature, one byte each for the number of beats per
bar and note value per beat, respectively:

| Hex    | Time  |
| :----- | :---- |
| `0204` | 2/4   |
| `0304` | 3/4   |
| `0404` | 4/4   |
| `0504` | 5/4   |
| `0604` | 6/4   |
| `0704` | 7/4   |
| `0508` | 5/8   |
| `0608` | 6/8   |
| `0708` | 7/8   |
| `0808` | 8/8   |
| `0908` | 9/8   |
| `0a08` | 10/8  |
| `0b08` | 11/8  |
| `0c08` | 12/8  |
| `0d08` | 13/8  |
| `0e08` | 14/8  |
| `0f08` | 15/8  |

### [Unknown]
```shell
0000
```

Two bytes of zeroes. Probably just padding.

## Parts list

After the initial 32 byte header, there appears to follow what I call
the "parts list", a list of six structures I call "part headers". Each
16 byte part header signifies the starting index and length in bars of
the intro, part 1, part 1 fill, part 2, part 2 fill, and ending.

Below, I'll use second parts header, i.e. the one for part 1, as an
example:

```shell
00000030: 0500 0400 8200 0000 0000 0000 b305 0000  ................
```

### Starting index

Two bytes of little-endian integer signifying the starting bar index
for the part. Note that the Boss RC-10R Rhythm Converter software
numbers the starting point from one, but the file format apparently
from zero.

In the example, the starting index is `0500`, meaning the part starts
at bar index 5, shown as bar 6 in the Rhythm Converter software.

### Length

Two bytes of little-endian integer signifying the length in bars for
the part. In the example above, the length is `0400`, i.e. 4, meaning
that this part consists of four bars.

### [Unknown]

The rest appears to always be the following 12 bytes:

```shell
8200 0000 0000 0000 b305 0000
```

The words `b305` and `8200` are 1459 and 130 respectively in decimal,
and could plausibly be offsets/lengths of some kind.

TODO: Poke around with these bytes, see if the Rhythm Converter
complains.

## MIDI data

TODO
