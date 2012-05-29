Full Frontal
============

A full front-end build suite to help prepare your assets for the web. Features include JavaScript, CSS and HTML minification and validation.

## Usage

The build script is controlled by configuration files containing JSON data. This means you should never need to edit the script itself. Keep reading for various examples to get your personal configuration file started.

Once you have setup your config file, running the build script is a matter of running a single simple command:

```ruby
rake "build_all[path/to/directory/with/config]"
```
    
## Configuration

Since Full Frontal is designed to be a complete front-end build tool, there are a multitude of options available to you, so you can get your output just as you like it. Your configuration file can contain up to three main sections, from the following:

`js` - Configuration for building JavaScript files

`css` - Configuration for building stylesheets

`html` - Configuration for building files containing markup

Each configuration section must contain an `ouput` property, and optionally and `options` property.

### Output files

Your configuration file specifies the result of the build process. Usually, you want to get some files out of that process (e.g. minified scripts). For this reason, each of your main config sections must contain an `output` property. This property should hold a map of output file names to inputs:

```javascript
{
  "js": {
    "output": {
      "mylibrary.min.js": {
        "input": [
          { "file": "module1.js" },
          { "file": "module2.js" }
        ]
      },
      "mydir/anotherlib.min.js": {
        "options": {
          "compiler": "yui"
        },
        "input": [
          { "file": "lib.js" }
        ]
      }
    }
  }
}
```

This example demonstrates several important points, which we will cover over the few sections. Notice how the `output` property can contain any number of output file names.

**Options**

Notice how only one of the output files specifies a set of `options`. Options can be specified at every level of the config file. If you want a set of options to apply to all output files, you can set them at the root level:

```javascript
{
  "js": {
    "options": {
      "compiler": "closure",
      "level": "advanced"
    },
    "output": //...
  }
}
```

If you want to apply different options to different output files, you can specify options at each `output` level. Note that options specified here will be merged with those specified higher up, with values from the more specific set overriding those from the less specific set:

```javascript
{
  "js": {
    "output": {
      "mylibrary.min.js": {
        "options": {
          "compiler": "yui"
        },
        "input": //...
      }
    }
  }
}
```

Finally, for even more control, you can specify options at the input file level. This allows you to give special treatment to files with certain requirements, without having to mess around and manually concatenate separately minified scripts:

```javascript
{
  "js": {
    "output": {
      "mylibrary.min.js": {
        "input": [{
          "file": "module1.js",
          "options": {
            "compiler": "closure",
            "level": "whitespace"
          }
         }, {
          "file": "module2.js"
         }]
      }
    }
  }
}
```

In this example, *module1.js* will be compiled according to the specific options applied to it, whereas *module2.js* will  be compiled with the default options (or options specified higher up the tree, if applicable). After compilation, the results will be concatentated into *mylibrary.min.js*.

**Input files**

Every output file must contain an `input` property. The `input` property holds a list of files to be compiled/minified and then concatenated into the output file. So, in the above example you would get two output files, relative to the path of your config file, `mylibrary.min.js` and `mydir/anotherlib.min.js`.

### JavaScript

<table>
    <thead>
        <tr>
            <th>Option</th>
            <th>Description</td>
            <th>Values</th>
            <th>Default</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>compiler</code></td>
            <td>The JavaScript compiler/minifier to use.</td>
            <td><code>closure | yui</code></td>
            <td><code>closure</code></td>
        </tr>
        <tr>
            <td><code>level</code></td>
            <td>The compilation level to apply (only applies to <code>closure</code> compiler)</td>
            <td><code>whitespace | simple | advanced</code></td>
            <td><code>simple</code></td>
        </tr>
    </tbody>
</table>