require "rake"
require "json"

task :build_all, [:config] do |t, args|
	config = JSON.parse(IO.read(args.config))
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
end

def minify_css(config)

end

def minify_html(config)

end