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

Each configuration section must contain an `ouput` property, and optionally and `options` property. The format of the value of that property depends on the section. See the documentation of each section for the details.

### JavaScript

JavaScript configuration is placed in the `js` property. The `js` property must contain an `output` property, and may contain an `options` property. If an `options` property is specified at this level, it will be applied to every file to be processed (although options can be overridden, as you will see). The `output` property must contain an `input` property. Here's a basic template:

```javascript
{
  "js": {
    "options": {
      "compiler": "closure",
      "level": "simple"
    },
    "output": {
      "mylibrary.min.js": {
        "input": [
          { "file": "module1.js" },
          { "file": "module2.js" }
        ]
      }
    }
  }
}
```

The above example specifies two input files, *module1.js* and *module2.js*, to be compiled and concatenated into *mylibrary.min.js*. As the `options` property is set at the root level, those options apply to each of the input files.

You can specify as many output targets as you like, each with as many inputs as necessary:

```javascript
{
  "js": {
    "options": {
      "compiler": "closure",
      "level": "simple"
    },
    "output": {
      "mylibrary.min.js": {
        "input": [
          { "file": "module1.js" },
          { "file": "module2.js" }
        ]
      },
      "anotherlib.min.js": {
        { "file": "unminified.js" }
      }
    }
  }
}
```

The above example will result in two minified files, *mylibrary.min.js* and *anotherlib.min.js*. Again, because `options` has been set at the root level, those options apply to all of the input files. If you want to specify different options for different output files, you can set the `options` property at any `output` level. Options set at this level will be merged with those set at the root level (if any), with the more specific values overriding the less specific. For example:

```javascript
{
  "js": {
    "options": {
      "compiler": "closure",
      "level": "simple"
    },
    "output": {
      "mylibrary.min.js": {
        "options": {
          "level": "advanced"
        },
        "input": [
          { "file": "module1.js" }
        ]
      }
    }
  }
}
```

In the above example, the `level` option will be overridden for each of the input files that make up *mylibrary.min.js*. The `compiler` option will not be overridden, so the value from the root level `options` will be used. If you need even finer control, it is also possible to specify options at the input level. Again, options set at this level will be merged with those set at the previous levels:

```javascript
{
  "js": {
    "options": {
      "compiler": "closure",
      "level": "simple"
    },
    "output": {
      "mylibrary.min.js": {
        "input": [
          { 
            "file": "module1.js",
            "options": {
              "level": "advanced"
            }
          },
          { "file": "module2.js" }
        ]
      }
    }
  }
}
```

In this example, *module1.js* will be compiled in `advanced` mode, since the `level` option overrides the `level` option set at the root level. However, *module2.js* will be compiled in `simple` mode, using the root level options, since no `options` property has been set on that input file. This means it's possible to minify different scripts in different ways, and still end up with them all in the same concatenated file.

**Options**

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
        <tr>
            <td><code>lint</code></td>
            <td>If present, the options to pass to JSLint (see JSLint options below)</td>
            <td><code>Array [String]</code></td>
            <td>undefined</td>
        </tr>
    </tbody>
</table>