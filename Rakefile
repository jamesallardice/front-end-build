###############################################################################################
# Configuration used throughout the script
# You probably only want to change these if you want to name your config file differently
###############################################################################################

# The name of the config file to load (in the directory passed to the task as an argument)
CONFIG_FILE = "build-conf.json"

# The name of the temporary directory (relative to this script) which will store intermediate files
TEMP = "temp"

###############################################################################################
# Paths to tools and utilities used throughout the build process
# You shouldn't need to edit these unless you want to use a different version of some tool
###############################################################################################

CLOSURE_PATH = "lib/compiler.jar"

YUI_PATH = "lib/yuicompressor-2.4.7.jar"

HTMLCOMPRESSOR_PATH = "lib/htmlcompressor-1.5.3.jar"

###############################################################################################
# End of configuration
# You shouldn't need to edit anything beyond this point
###############################################################################################

require "rake"
require "json"

task :build_all, [:config] do |t, args|
	$config_path = args.config
	config = JSON.parse(IO.read(File.join(args.config, CONFIG_FILE)))
	Dir.mkdir(TEMP) unless File.exists?(TEMP)
	config.each{ |k, v|
		case k
		when "js"
			minify_js(v)
		when "css"
			minify_css(v)
		when "html"
			minify_html(v)
		end
	}
end

CLOSURE_LEVELS = {
	"whitespace" => "WHITESPACE_ONLY",
	"simple" => "SIMPLE_OPTIMIZATIONS",
	"advanced" => "ADVANCED_OPTIMIZATIONS"
}

# TODO: Add ability to specify `--externs` options to Closure compiler
# TODO: Add ability to specify `--line-break`, `--nomunge`, `--preserve-semi` and `--disable-optimizations` arguments to YUI
def minify_js(config)
	puts "\nCompressing JavaScript files"
	options = {
		"compiler" => "closure",
		"level" => "simple",
		"lint" => false
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
			if inputOptions["lint"]
				validate_js(file, inputOptions["lint"])
			end
			puts "Compressing #{input['file']}..."
			compiled = File.join(TEMP, "#{i}.js")
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
end

def validate_js(file, options)

end

# TODO: Look at other potential CSS minification tools (YUI is currently the only option in this script)
# TODO: Investigate a reasonable line length for compressed file (500 has been plucked out of the air)
def minify_css(config)
	puts "\nCompressing CSS files"
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
			puts "Compressing #{input['file']}..."
			inputOptions = input.key?("options") ? outputOptions.merge(input["options"]) : outputOptions
			file = File.join($config_path, input["file"])
			compiled = File.join(TEMP, "#{i}.css")
			outputFiles << compiled
			system "java -jar #{YUI_PATH} --type css --line-break #{options['linebreak']} -o #{compiled} #{file}"
		}
		concat(outputFiles, File.join($config_path, name))
	}
end

# TODO: Add ability to pass options to HtmlCompressor
# TODO: Investigate any other HTML compression tools (HtmlCompressor is currently the only option in this script)
# TODO: Investigate possibility of adding ability to concatenate HTML files (currently each file is compressed and that's it)
def minify_html(config)
	puts "\nCompressing files containing HTML"
	outputdir = config["outputdir"]
	config["output"].each{ |input|
		puts "Compressing #{input['file']}..."
		file = File.join($config_path, input["file"])
		compressed = File.join($config_path, outputdir, input["file"])
		system "java -jar #{HTMLCOMPRESSOR_PATH} -t html -o #{compressed} #{file}"
	}
end

def concat(files, output)
	File.open(output, "w") { |file|
		file.puts files.map { |s|
			IO.read(s)
		}
	}
end