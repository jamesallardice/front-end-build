Full Frontal
============

A full front-end build suite to help prepare your assets for the web. Features include JavaScript, CSS and HTML minification and validation.

## Installation

To get up and running, ensure the following are available on your system:

 - [Ruby](http://www.ruby-lang.org/en/downloads/)
 - [Ruby JSON gem](http://flori.github.com/json/)
 - [Java](http://java.com/en/download/index.jsp)

## Usage

The build script is controlled by configuration files containing JSON data. This means you should never need to edit the script itself. Keep reading for various examples to get your personal configuration file started.

Once you have setup your config file, running the build script is a matter of running a single simple command:

```ruby
rake "build_all[path/to/directory/with/config]"
```
    
## Configuration

Since Full Frontal is designed to be a complete front-end build tool, there are a multitude of options available to you, so you can get your output just as you like it. Your configuration file can contain up to three main sections, from the following:

[`js` - Configuration for building JavaScript files](https://github.com/jamesallardice/full-frontal/wiki/JavaScript-Configuration)

`css` - Configuration for building stylesheets

`html` - Configuration for building files containing markup

Each configuration section must contain an `ouput` property, and optionally and `options` property. The format of the value of that property depends on the section. See the documentation of each section for the details.