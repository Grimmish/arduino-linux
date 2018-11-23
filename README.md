# arduino-linux
Tools and hints for the occasional Arduino hacker

## How everything works
This is primarily for my own benefit, as I tend to forget all of this after a year away. Hello future me!

### Hardware
Supported hardware is defined in `boards.txt` files:

| Source | Path |
| --- | --- |
| IDE built-in | `<IDE root>/hardware/<vendor>/<arch>/boards.txt` |
| Extra packages | `~/.arduino15/packages/<vendor>/hardware/<arch>/<version>/boards.txt` |

Fire up the Arduino IDE GUI to find & download more hardware support: Tools >> Boards >> Boards Manager

Hardware strings break down as `<vendor>:<arch>:<board>`. Examples:
```
  adafruit:avr:protrinket5
  arduino:avr:uno
  adafruit:itsybitsy32u4_3V
```
#### Tools
List all valid hardware strings built into the IDE:
```
perl -pe 's/^.*\/hardware\/(\w+)\/(\w+)\/boards\.txt:(\w+)\.name=/$1:$2:$3 -- /' <(fgrep '.name=' `find <ARDUINO_IDE_PATH> -name boards.txt`)
```
List hardware strings available from additional installed packages:
```
perl -pe 's/^.*\/packages\/(\w+)\/hardware\/(\w+)\/.*boards\.txt:(\w+)\.name=/$1:$2:$3 -- /' <(fgrep '.name=' `find ~/.arduino15 -name boards.txt`)
```
