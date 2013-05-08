require 'open-uri'
require 'json'
require 'erb'
require 'cgi'

URL   = 'https://api.github.com/users/blackwinter/repos?page=%d'
FILES = %w[index.html]

task :default => %w[update]
task :update  => %w[clean files]
task :files   => FILES

task :clean do
  FILES.each { |file| File.unlink(file) if File.exists?(file) }
end

file 'index.html' do
  repos, page, template = [], 0, File.read('index.html.erb')

  loop {
    r = JSON.parse(open(URL % page += 1).read, symbolize_names: true)
    r.empty? ? break : repos.concat(r)
  }

  def h(string)
    CGI.escapeHTML(string)
  end

  result = ERB.new(template).result(binding)
  File.open('index.html', 'w') { |f| f.puts result } unless result.empty?
end
