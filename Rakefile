CONFIG_FILE = "build-conf.json"

TEMP = "temp"

CLOSURE_PATH = "lib/compiler.jar"

require "rake"
require "json"

task :build_all, [:config] do |t, args|
	$config_path = args.config
	config = JSON.parse(IO.read(args.config + "/#{CONFIG_FILE}"))
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

def minify_js(config)
	options = {
		"compiler" => "closure",
		"level" => "simple"
	}
	if config.key?("options")
		options = options.merge(config["options"])
	end
	config["output"].each{ |name, output|
		outputOptions = output.key?("options") ? options.merge(output["options"]) : options
		output["input"].each_with_index{ |input, i|
			inputOptions = input.key?("options") ? outputOptions.merge(input["options"]) : outputOptions
			file = File.join($config_path, input["file"])
			compiled = File.join(TEMP, "#{i}.js")
			case inputOptions["compiler"]
			when "closure"
				system "java -jar #{CLOSURE_PATH} --js #{file} --js_output_file #{compiled}"
			end
		}
	}
end

def minify_css(config)

end

def minify_html(config)

end