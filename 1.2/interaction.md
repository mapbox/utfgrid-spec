# Interaction

Tile servers can enhance tilesets with interactivity by implementing two additional HTTP endpoints.

* `[base path]/layer.json`: A layer manifest JSON containing the interaction formatter function and other optional attributes.
* `[base path]/{n}/{n}/{n}.grid.json`: A UTFGrid JSON file corresponding to its adjacent tile image.

`[base path]` refers to the full layer URL prior to any x, y or z coordinates.

Examples:

OSM-style URL schema

    http://example.com/0/0/0.png       // tile image for 0/0/0
    http://example.com/0/0/0.grid.json // utfgrid for 0/0/0
    http://example.com/layer.json      // layer manifest

## TileJSON

UTFGrid requires additions to the [TileJSON](https://github.com/mapbox/tilejson-spec)
payload for a layer:

* `template`: String. In the format of a mustache template.
* `legend`: String. Self-contained HTML that may be displayed as a legend for
  this layer. **Optional**.

Example response from `layer.json`:

    {
        "template": "{{NAME}}",
        "legend": "<strong>Countries of the World</strong>"
    }

Each `layer.json` item should be represented by a single row in the `metadata` table where `key,value` match its key and value in the `layer.json` object.

### Template

As of UTFGrid 1.1, the `formatter` key is deprecated and replaced by `template`.
Template is to be a [mustache](http://mustache.github.com/) format string that
produces
HTML, which will be cleaned with an HTML whitelist after generation.

#### Mustache

Template data is specified according to the
[mustache specification](http://mustache.github.com/mustache.5.html).
The full specification is supported, but no partials are provided,
or should be provided by implementations.

Given the switch to templates from formatters, the `options` object
is no longer available. Its functionality is emulated by setting
'format flags' on each data object.

For an example data object like


    {
        "id": "helloworld"
    }


This will be transformed into


    {
        "id": "helloworld"
        "__location__": true
    }


By the tooltip/interaction implementation, in order to trigger
the `location` template. Note that `true`, `1`, and all non-false
values are equal to template, so implementations may set
`"__location__": 1` to save bytes.

The template implementation could be:


    {{#__location__}}
        http://your.com/{{id}}
    {{/__location__}}
    {{#__full__}}
        This content has the id {{id}}
    {{/__full__}}
    {{#__teaser__}}
        {{id}}
    {{/__teaser__}}


Which, for this implementation, will produce the output


    http://your.com/helloworld


### legend

A tileset may provide an HTML string that can be rendered by the client as a legend. The string should be self-contained and not reference external stylesheets, scripts or images. The [Data URI scheme](http://en.wikipedia.org/wiki/Data_URI_scheme) may be used to embed images or other data if necessary.

    <div><span style='padding:0px 10px; background:#333;'></span> +10% population</div>
    <div><span style='padding:0px 10px; background:#666;'></span> +5% population</div>
    <div><span style='padding:0px 10px; background:#999;'></span> +0% population</div>
    <div><span style='padding:0px 10px; background:#ccc;'></span> -5% population</div>

## grid.json

See `utfgrid.md` for the format and storage of UTFGrid JSON.

