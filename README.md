Front End Build
============

A full front-end build suite to help prepare your assets for the web by reducing file sizes and improving confidence in your code. Features include JavaScript, CSS and HTML minification and validation.

Front End Build is for you if you want a quick and easy way to decrease the loading times of your web pages. It is designed as a basic, all-encompasing build tool to get you up and running in as little time as possible. It has been developed with a specific web app in mind, so it may not be exactly what you're looking for, but it should be easy enough to modify to suit your needs.

## Installation

To get up and running, ensure the following are available on your system:

 - [Ruby](http://www.ruby-lang.org/en/downloads/) (see [RubyInstaller](http://rubyinstaller.org/) for a Windows installer)
 - [Ruby JSON gem](http://flori.github.com/json/)
 - [Java](http://java.com/en/download/index.jsp)

Front End Build has been tested on OS X Lion and Windows 7. It should run on any system that supports the above technologies.

## Usage

The build script is controlled by configuration files containing JSON data. This means you should never need to edit the script itself. Keep reading for various examples to get your personal configuration file started.

Once you have setup your config file, running the build script is a matter of running a single simple command:

```ruby
rake "build[path/to/directory/with/config]"
```

Alternatively, place the *Rakefile* in the same directory as your config file, and simply run the task with no arguments:

```ruby
rake build
```
    
## Configuration

Since Front End Build is designed to be a complete front-end build tool, there are a multitude of options available to you, so you can get your output just as you like it. These options are all set in a single configuration file. By default, the build script looks for a file called *build.json*, in its own directory.

Configuration files are made up of lists of tasks. These tasks are executed in the order you define them. If you know any JavaScript, or have ever worked with JSON, then you should find it nice and easy to set up your config file. Here's a very simple example. We will come on to the tasks available to you soon.

```javascript
{
    "root": "../src",
    "tasks": [
        {
            "task": "taskname",
            "options": {
                "someoption": true
            }
        }
    ]
}
```

### The root directory

You can specifiy a root directory by including a `root` property in your configuration file. The basic example above includes an example `root` property. When you specifiy a root directory, all other paths in the configuration file will be interpreted relative to the root path. This makes it easy for you to place your configuration file in a directory that is far away from your actual assets.

If you do not specify a root directoy, all paths will be interpreted relative to the configuration file itself.

### Tasks

A number of tasks are available for use in your configuration file. Each task has various options and details specific to that task, so visit the Wiki pages for more information.

[`validatejs` - Runs a JavaScript validation tool such as JSLint](https://github.com/jamesallardice/front-end-build/wiki/The-'validatejs'-task)

[`validatecss` - Runs a CSS validation tool such as CSSLint](https://github.com/jamesallardice/front-end-build/wiki/The-'validatejs'-task)

[`minifyjs` - Minify and combine JavaScript files to reduce file size](https://github.com/jamesallardice/front-end-build/wiki/The-'minifyjs'-task)

[`minifycss` - Minify and combine CSS files to reduce file size](https://github.com/jamesallardice/front-end-build/wiki/The-'minifycss'-task)

[`minifyhtml` - Minify and combine files containing markup](https://github.com/jamesallardice/front-end-build/wiki/The-'minifyhtml'-task)

### Output

Chances are that the main thing you want to get out of your build script is a set of files. Front End Build tries to be as flexible as possible and allows you to specify exactly what files you want it to produce (unfortunately this means your config file can become quite verbose for larger projects, but that's the price of flexibility). Tasks that can produce output should be given an `output` property. Here's a basic example using the `minifyjs` task to compress some JavaScript files:

```javascript
{
    "tasks": [
        {
            "task": "minifyjs",
            "output": {
                "mylibrary.min.js": {
                    "input": [
                        { "file": "module1.js" },
                        { "file": "module2.js" },
                        { "file": "includes/include1.js" }
                    ]
                },
                "lib2/mylibrary2.min.js": {
                    "input": [
                        { "file": "lib2/main.js" }
                    ]
                }
            }
        }
    ]
}
```

This example will produce 2 files, relative to the configuration file. *mylibrary.min.js* will be made up of the minified versions of *module1.js*, *module2.js* and *includes/include1.js*, all concatenated together. *lib2/mylibrary2.min.js* will be made up of just the one file, *lib2/main.js*.

## Bundled tools

The Front End Build script depends on a number of third party tools. Everything that is required is bundled into the download packages, and can be found in the *lib* directory. Included tools are as follows:

<table>
<thead>
<tr>
<th>Tool</th>
<th>Version</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="http://www.mozilla.org/rhino/">Rhino</a></td>
<td>1.7R3</td>
<td>An implementation of JavaScript written in Java. Used to run various other tools.</td>
</tr>
<tr>
<td><a href="http://code.google.com/p/jslint4java/">JSLint4Java</a></td>
<td>2.0.2</td>
<td>A Java wrapper for JSLint. Used to validate JavaScript.</td>
</tr>
<tr>
<td><a href="http://www.jshint.com/">JSHint Rhino</a></td>
<td>Stable</td>
<td>A JavaScript validation tool that runs on Rhino.</td>
</tr>
<tr>
<td><a href="http://csslint.net/">CSSLint Rhino</a></td>
<td>0.9.8</td>
<td>A CSS validation tool that runs on Rhino.</td>
</tr>
<tr>
<td><a href="https://developers.google.com/closure/compiler/">Google Closure Compiler</a></td>
<td>r1918</td>
<td>A JavaScript compiler that minifies and speeds up your JavaScript.</td>
</tr>
<tr>
<td><a href="http://developer.yahoo.com/yui/compressor/">YUI Compressor</a></td>
<td>2.4.7</td>
<td>A JavaScript and CSS minification tool.</td>
</tr>
<tr>
<td><a href="http://code.google.com/p/htmlcompressor/">HtmlCompressor</a></td>
<td>1.5.3</td>
<td>An HTML and XML minification tool.</td>
</tr>
</tbody>
</table>