require 'open-uri'
require 'json'
require 'erb'
require 'cgi'

user = ENV['GITHUB_USER'] || %x{git config github.user}.chomp

url = "https://api.github.com/users/#{user}/repos?page=%d"
htm = 'index.html'
erb = htm + '.erb'

OPT = { 'Accept' => 'application/vnd.github.drax-preview+json' }

task default: htm

def api(url)
  JSON.parse(URI.open(url, OPT).read, symbolize_names: true)
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

  def a(name, href)
    %Q{<a href="#{h(href)}">#{h(name)}</a>}
  end

  def v(repo, *keys)
    v = repo.dig(*keys).to_s
    "[#{block_given? ? yield(v) : h(v)}]" unless v.empty?
  end

  result = ERB.new(template).result(binding)
  File.open(htm, 'w') { |f| f.puts result } unless result.empty?
end
