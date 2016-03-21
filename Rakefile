require 'open-uri'
require 'json'
require 'erb'
require 'cgi'

url = 'https://api.github.com/users/blackwinter/repos?page=%d'
htm = 'index.html'
erb = htm + '.erb'

task default: htm

def api(url)
  JSON.parse(open(url).read, symbolize_names: true)
end

file htm => erb do
  repos, page, template = [], 0, File.read(erb)

  loop {
    result = api(url % page += 1)
    result.empty? ? break : repos.concat(result)
  }

  repos.each { |repo|
    repo[:parent] ||= api(repo[:url])[:parent] if repo[:fork]
  }

  def h(string)
    CGI.escapeHTML(string)
  end

  result = ERB.new(template).result(binding)
  File.open(htm, 'w') { |f| f.puts result } unless result.empty?
end
