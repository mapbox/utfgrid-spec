# UTFGrid

The UTFGrid encoding scheme encodes interactivity data for a tile in a space
efficient manner. It is designed to be used in browsers, e.g. for displaying
tooltips when hovering over certain features of a map tile.

Since slower browsers and machines can't cope with rendering the actual
polygons used to draw vectors on the map tile, we use a grid-based approach
where we store the associated information for each pixel.

UTFGrid uses [JSON](http://en.wikipedia.org/wiki/JSON) as a container format.
It is exclusively geared towards square tiles.

## Grid

To achieve reasonable speed in browsers, we store information for a pixel in
long strings, where each character's Unicode code point is the key for retrieving
the information associated with that pixel. When we have less than 96 unique
IDs, this means that the space taken up by storing each pixel separately is
256 * 256 = 64 KB. Gzipping the grid data typically reduces it to a size below 2K.

By default, UTFGrid operates on a 4x4 grid, meaning that a tile at zoom level
0 that contains the entire world in its extent will have an grid resolution
of 64x64. We take advantage of UTF-8's variable length codepoint encoding:
all ASCII characters are encoded as is, that means that the first 94 codepoints
are encoded with their code number as a single byte (codes `0x20`, `0x21`,
`0x23`-`0x5B` and `0x5D`-`0x7F`).
IDs with a number larger than that will get encoded as multiple bytes.

## Encoding IDs

[JSON](http://www.ietf.org/rfc/rfc4627) doesn't allow control characters, `"`
and `\` to be encoded as their literal UTF-8 representation. **Encoding** an
ID works as follows:

* Add 32<sub>10</sub>.
* If the result is >= 34<sub>10</sub>, add 1.
* If the result is >= 92<sub>10</sub>, add 1.

This ensures that all characters that cannot be represented natively are skipped.

**Decoding** works as follows:

* If the codepoint is >= 93<sub>10</sub>, subtract 1.
* If the codepoint is >= 35<sub>10</sub>, subtract 1.
* Subtract 32<sub>10</sub>.

## Mapping an ID to a key

The UTFgrid file contains an array in a property named `grid` at the root level.
Each entry represents a row in the grid. Each array entry is a string that
contains the UTF-8 encoded codepoint for each column. The string length
corresponds to the number of entries in the `grid` array. Only powers of two are allowed.

The keys are stored in an array named `keys` at the root level. The index of
each key represents the ID that it is associated to.

Retrieving a key from a coordinate works as follows (`json` is the root level
object, `x` and `y` are the coordinates, starting from top left at 0, and
`size` is the number of entries in the `grid` key):

* `var factor = 256 / size, row = y / factor, col = x / factor`
* `var id = json.grid[row].charCodeAt(col);` is the character that contains the encoded ID.
* Decode the id as described in *"Encoding IDs"*.
* `var key = json.keys[id];` retrieves the ID associated with the coordinate.

All divisions are integer divisions.

## Mapping a key to data

The JSON file may contain an optional `data` property at the root level.
If it isn't present, the client looks up the obtained key in its internal data
store. If the lookup key is not present, it queries the server with the
missing keys. If the `data` property is present, but the key cannot be found,
the client must behave as if there were no `data` property.

An empty key signifies the unavailability of information for that pixel.
No action may be taken to retrieve data for an empty (`""`) key.


## Example UTFGrid JSON file

```json
{ "grid":
[ "                                                    !!!#########",
  "                                                    !!!#########",
  "                                                   !!!!#########",
  "                                                   !!!##########",
  "                        !!                         !!!##########",
  "                                                    !!!#########",
  "                                                    !!######### ",
  "                            !                      !!! #######  ",
  "                                                       ###      ",
  "                                                        $       ",
  "                                                        $$    %%",
  "                                                       $$$$$$$%%",
  "                                                       $$$$$$$%%",
  "                                                     $$$$$$$$$%%",
  "                                                    $$$$$$$$$$%%",
  "                                                   $$$$$$$$$$$$%",
  "                                                   $$$$$$$$$%%%%",
  "                                                  $$$$$$$$$%%%%%",
  "                                                  $$$$$$$$%%%%%%",
  "                                                  $$$$$$$%%%%%%%",
  "                                                  $$$$%%%%%%%%%%",
  "                                                 $$$$%%%%%%%%%%%",
  "                                        # # #  $$$$$%%%%%%%%%%%%",
  "                                             $$$$$$$%%%%%%%%%%%%",
  "                                             $$$&&&&'%%%%%%%%%%%",
  "                                            $$$$&&&&'''%%%%%%%%%",
  "                                           $$$$'''''''''%%%%%%%%",
  "                                           $$$$'''''''''''%%%%%%",
  "                                          $$$$&''''''''((((%%%%%",
  "                                          $$$&&''''''''(((((%%%%",
  "                                         $$$&&'''''''''(((((((%%",
  "                                         $$$&&''''''''''(((((((%",
  "                                        $$$&&&''''''''''((((((((",
  "                                        ''''''''''''''''((((((((",
  "                                         '''''''''''''''((((((((",
  "                                         '''''''''''''''((((((((",
  "                                         '''''''''''''''((((((((",
  "                                         '''''''''''''''((((((((",
  "                                         '''''''''''''''((((((((",
  "                            )            '''''''''''''''((((((((",
  "                                         ***'''''''''''''(((((((",
  "                                         *****'''''''''''(((((((",
  "                              ))        ******'''(((((((((((((((",
  "                                        *******(((((((((((((((++",
  "                                        *******(((((((((((((++++",
  "                                        ********((((((((((((++++",
  "                                        ***,,-**((((((((((++++++",
  "                                         ,,,,-------(((((+++++++",
  "                                         ,,---------(((((+++++.+",
  "                                            --------(((((+++....",
  "                                             -///----0000000....",
  "                                             ////----0000000....",
  "                                             /////1---0000000...",
  "                                              ///11--0000000....",
  "                                                111110000000....",
  "                                                 11110000000....",
  "                                                  1111000000....",
  "                                                    1100     .  ",
  "                                                                ",
  "                                                                ",
  "                                                                ",
  "                                                                ",
  "                                                                ",
  "                                                                " ],
"keys":
 [ "",
   "1",
   "2",
   "3",
   "4",
   "5",
   "6",
   "7",
   "8",
   "9",
   "10",
   "11",
   "12",
   "13",
   "14",
   "15",
   "16" ],
"data":
 { "1": { "admin": "Portugal" },
   "2": { "admin": "Spain" },
   "3": { "admin": "Morocco" },
   "4": { "admin": "Algeria" },
   "5": { "admin": "Western Sahara" },
   "6": { "admin": "Mauritania" },
   "7": { "admin": "Mali" },
   "8": { "admin": "Cape Verde" },
   "9": { "admin": "Senegal" },
   "10": { "admin": "Burkina Faso" },
   "11": { "admin": "Guinea Bissau" },
   "12": { "admin": "Guinea" },
   "13": { "admin": "Ghana" },
   "14": { "admin": "Sierra Leone" },
   "15": { "admin": "Ivory Coast" },
   "16": { "admin": "Liberia" } } }
```

To test implementations, [`demo.json`](https://github.com/mapbox/mbtiles-spec/blob/master/1.1/demo.json) contains a grid that consists of 65501 different keys. This is the maximum possible in this version of UTFGrid. Implementors should check that obtaining a coordinate should return the key `y * 256 + x` for all x/y, with the exception of y = 255 and x >= 222 and x <= 255 returning 65501 due to the maximum codepoint allowed in JSON.

A dummy code validation routine is given here:

```javascript
var json = JSON.parse(/* demo.json */);

// the resolution of the grid. adjust this for your grid.
var resolution = 4;

var key = 0,
    dimension = 256 / resolution;

for (var y = 0; y < dimension; y++) {
    for (var x = 0; x < dimension; x++) {
        var code = json.grid[y].charCodeAt(x);
        if (code >= 93) code--;
        if (code >= 35) code--;
        code -= 32;

        assert(code == key);

        if (key < 65501) key++;
    }
}
```
