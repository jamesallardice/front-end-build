###############################################################################################
# Configuration used throughout the script
# You probably only want to change these if you want to name your config file differently
###############################################################################################

# The name of the config file to load (in the directory passed to the task as an argument)
CONFIG_FILE = "build.json"

# The name of the temporary directory (relative to this script) which will store intermediate files
TEMP = "temp"

###############################################################################################
# Paths to tools and utilities used throughout the build process
# You shouldn't need to edit these unless you want to use a different version of some tool
###############################################################################################

CLOSURE_PATH = "lib/compiler.jar"

YUI_PATH = "lib/yuicompressor-2.4.7.jar"

HTMLCOMPRESSOR_PATH = "lib/htmlcompressor-1.5.3.jar"

JSLINT_PATH = "lib/jslint4java-2.0.2.jar"

JSHINT_PATH = "lib/jshint-rhino.js"

CSSLINT_PATH = "lib/csslint-rhino.js"

RHINO_PATH = "lib/rhino.jar"

###############################################################################################
# End of configuration
# You shouldn't need to edit anything beyond this point
###############################################################################################

require "rake"
require "json"

OUTPUT_SEPARATOR = "----------------------------\n\n"

CLOSURE_LEVELS = {
	"whitespace" => "WHITESPACE_ONLY",
	"simple" => "SIMPLE_OPTIMIZATIONS",
	"advanced" => "ADVANCED_OPTIMIZATIONS"
}

def concat(files, output)
	File.open(output, "w") { |file|
		file.puts files.map { |s|
			IO.read(s)
		}
	}
end

# TODO: Add ability to specify `--externs` options to Closure compiler
# TODO: Add ability to specify `--line-break`, `--nomunge`, `--preserve-semi` and `--disable-optimizations` arguments to YUI
minifyjs = lambda { |config|
	puts "\nMinifying JavaScript files"
	puts OUTPUT_SEPARATOR
	options = {
		"compiler" => "closure",
		"level" => "simple"
	}
	if config.key?("options")
		options = options.merge(config["options"])
	end
	closureLevel = CLOSURE_LEVELS[options["level"]]
	config["output"].each{ |name, output|
		outputOptions = output.key?("options") ? options.merge(output["options"]) : options
		outputFiles = Array.new
		output["input"].each_with_index{ |input, i|
			inputOptions = input.key?("options") ? outputOptions.merge(input["options"]) : outputOptions
			file = File.join($config_path, input["file"])
			compiled = File.join(TEMP, "#{i}.js")
			puts "Compressing #{input['file']}..."
			outputFiles << compiled
			case inputOptions["compiler"]
			when "closure"
				system "java -jar #{CLOSURE_PATH} --compilation_level #{closureLevel} --js #{file} --js_output_file #{compiled}"
			when "yui"
				system "java -jar #{YUI_PATH} --type js -o #{compiled} #{file}"
			end
		}
		concat(outputFiles, File.join($config_path, name))
	}
}

validatejs = lambda { |config|
	puts "\nValidating JavaScript files"
	puts OUTPUT_SEPARATOR
	options = {
		"validator" => "jslint"
	}
	if config.key?("options")
		options = options.merge(config["options"])
	end
	config["files"].each{ |file|
		fileOptions = file.key?("options") ? options.merge(file["options"]) : options
		puts "Validating #{file['file']}..."
		path = File.join($config_path, file["file"])
		args = ""
		case fileOptions["validator"]
		when "jslint"
			fileOptions["jslintoptions"].each{ |option|
				args << " --#{option}"
			}
			results = `java -jar #{JSLINT_PATH} #{args} #{path}`
		when "jshint"
			results = `java -jar #{RHINO_PATH} #{JSHINT_PATH} #{path}`
		else
			puts "\nUnknown JavaScript validator. Available options are 'jslint' and 'jshint'.\n\n"
			exit 1
		end
		if !results.empty?
			puts "\t#{results}"
			puts "\n\tJavaScript validation failed.\n\n"
			exit 1
		else
			puts "\tPassed!"
		end
	}
}

# TODO: Look at other potential CSS minification tools (YUI is currently the only option in this script)
# TODO: Investigate a reasonable line length for compressed file (500 has been plucked out of the air)
minifycss = lambda { |config| 
	puts "\nMinifying CSS files"
	puts OUTPUT_SEPARATOR
	options = {
		"linebreak" => 500
	}
	if config.key?("options")
		options = options.merge(config["options"])
	end
	config["output"].each{ |name, output|
		outputOptions = output.key?("options") ? options.merge(output["options"]) : options
		outputFiles = Array.new
		output["input"].each_with_index{ |input, i|
			inputOptions = input.key?("options") ? outputOptions.merge(input["options"]) : outputOptions
			puts "Compressing #{input['file']}..."
			file = File.join($config_path, input["file"])
			compiled = File.join(TEMP, "#{i}.css")
			outputFiles << compiled
			system "java -jar #{YUI_PATH} --type css --line-break #{options['linebreak']} -o #{compiled} #{file}"
		}
		concat(outputFiles, File.join($config_path, name))
	}
}

# TODO: Investigate any other CSS lint tools (CSSLint is currently the only option in this script)
validatecss = lambda { |config|
	puts "\nValidating CSS files"
	puts OUTPUT_SEPARATOR
	options = {
		"validator" => "csslint"
	}
	if config.key?("options")
		options = options.merge(config["options"])
	end
	config["files"].each{ |file|
		fileOptions = file.key?("options") ? options.merge(file["options"]) : options
		puts "Validating #{file['file']}..."
		args = ""
		fileOptions["csslintoptions"].each{ |option|
			args << "#{option},"
		}
		path = File.join($config_path, file["file"])
		results = `java -jar #{RHINO_PATH} #{CSSLINT_PATH} #{path} --rules=#{args}`
		noerror = /csslint\: No errors/
		if !(noerror =~ results)
			puts "\t#{results}"
			puts "\n\tCSS validation failed.\n\n"
			exit 1
		else
			puts "\tPassed!"
		end
	}
}

# TODO: Add ability to pass options to HtmlCompressor
# TODO: Investigate any other HTML compression tools (HtmlCompressor is currently the only option in this script)
minifyhtml = lambda { |config|
	puts "\nMinifying markup files"
	puts OUTPUT_SEPARATOR
	options = {}
	outputdir = config["outputdir"]
	config["output"].each{ |name, output|
		outputOptions = output.key?("options") ? options.merge(output["options"]) : options
		outputFiles = Array.new
		extension = File.extname(name)
		output["input"].each_with_index{ |input, i|
			inputOptions = input.key?("options") ? outputOptions.merge(input["options"]) : outputOptions
			puts "Compressing #{input['file']}..."
			file = File.join($config_path, input["file"])
			compiled = File.join(TEMP, "#{i}#{extension}")
			outputFiles << compiled
			system "java -jar #{HTMLCOMPRESSOR_PATH} -t html -o #{compiled} #{file}"
		}
		concat(outputFiles, File.join($config_path, name))
	}
}

tasks = {
	"minifyjs" => minifyjs,
	"validatejs" => validatejs,
	"minifycss" => minifycss,
	"validatecss" => validatecss,
	"minifyhtml" => minifyhtml
}

task :build_all, [:config] do |t, args|
	$config_path = args.config
	config = JSON.parse(IO.read(File.join(args.config, CONFIG_FILE)))
	Dir.mkdir(TEMP) unless File.exists?(TEMP)
	config.each{ |v|
		tasks[v["task"]].call(v)
	}
end