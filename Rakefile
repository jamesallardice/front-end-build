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

def minify_js(options)

end

def minify_css(options)

end

def minify_html(options)

end