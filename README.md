# arduino-linux
Tools and hints for the occasional Arduino hacker.

This is primarily for my own benefit, as I tend to forget all of this after a year away. Hello future me!

## Uploading
Arduino IDE >= v1.5 has good CLI support:
```
  ./arduino --upload --board <HARDWARE_STRING> --port /dev/<SOMETHING> <PROJECTNAME>/<PROJECTNAME>.ino
```
The IDE will expect to find your code in the sketchbook folder defined in `~/.arduino15/preferences.txt` in the `sketchbook.path` property. E.g. for `sketchbook.path=/foo`, your code is in `/foo/myproject/myproject.ino`.

Lots more info: <https://github.com/arduino/Arduino/blob/master/build/shared/manpage.adoc>

## Hardware
Supported hardware is defined in `boards.txt` files:

| Source | Path |
| --- | --- |
| IDE built-in | `<IDE root>/hardware/<vendor>/<arch>/boards.txt` |
| Extra packages | `~/.arduino15/packages/<vendor>/hardware/<arch>/<version>/boards.txt` |

Fire up the Arduino IDE GUI to find & download more hardware support: Tools >> Boards >> Boards Manager

Hardware strings break down as `<package>:<arch>:<board>`. Examples:
```
  adafruit:avr:protrinket5
  arduino:avr:uno
  adafruit:itsybitsy32u4_3V
```
### Tools
List all valid hardware strings built into the IDE:
```
perl -pe 's/^.*\/hardware\/(\w+)\/(\w+)\/boards\.txt:(\w+)\.name=/$1:$2:$3 -- /' <(fgrep '.name=' `find <ARDUINO_IDE_PATH> -name boards.txt`)
```
List hardware strings available from additional installed packages:
```
perl -pe 's/^.*\/packages\/(\w+)\/hardware\/(\w+)\/.*boards\.txt:(\w+)\.name=/$1:$2:$3 -- /' <(fgrep '.name=' `find ~/.arduino15 -name boards.txt`)
```
## Libraries
Built-in libraries are in `<IDE root>/libraries`; libraries can also live in the `libraries/` subfolder in your sketchbook path.

## Notes for specific boards
- **trinket/protrinket** :: These do not have serial support and rely on a weird USB bit-bang programming protocol (the "usbtiny" programmer), so no `--port` option is needed for uploading. Also, you will need to push the reset button to put them in programming mode just before you run the uploader.

## Under the hood of the Arduino CLI
When it's not rendering the GUI, the Arduino executable is basically just wrangling a few low-level tools based on the hardware settings expressed in `boards.txt`. Use the `-v` option to see exactly how it's executing each stage:
- **avr-g++** :: Compiling, linking, rendering hex files
- **stty** :: Arduino-compatible boards with serial support can be bounced into programming mode by sending a DTR bump at 1200 bps: `stty -F /dev/ttyACM0 speed 1200 hupcl`
- **avrdude** :: Flashes the compiled hex code onto the chip
