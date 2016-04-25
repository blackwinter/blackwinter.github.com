require 'open-uri'
require 'json'
require 'erb'
require 'cgi'

user = ENV['GITHUB_USER'] || %x{git config github.user}.chomp

url = "https://api.github.com/users/#{user}/repos?page=%d"
htm = 'index.html'
erb = htm + '.erb'

task default: htm

def api(url)
  JSON.parse(open(url).read, symbolize_names: true)
end

task htm => erb do
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
