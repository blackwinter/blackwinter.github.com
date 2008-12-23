require 'open-uri'
require 'erb'
require 'cgi'

task :default => %w[index.html]

file 'index.html' do
  url = 'http://github.com/api/v1/yaml/blackwinter'
  repos = YAML.load(open(url))['user']['repositories']
  template = File.read('index.html.erb')

  def h(string)
    CGI.escapeHTML(string)
  end

  result = ERB.new(template).result(binding)
  File.open('index.html', 'w') { |f| f.puts result } unless result.empty?
end
