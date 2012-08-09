require 'open-uri'
require 'json'
require 'erb'
require 'cgi'

URL   = 'https://api.github.com/users/blackwinter/repos'
FILES = %w[index.html]

task :default => %w[update]
task :update  => %w[clean files]
task :files   => FILES

task :clean do
  FILES.each { |file|
    File.unlink(file) if File.exists?(file)
  }
end

file 'index.html' do
  repos    = JSON.parse(open(URL).read, symbolize_names: true)
  template = File.read('index.html.erb')

  def h(string)
    CGI.escapeHTML(string)
  end

  result = ERB.new(template).result(binding)
  File.open('index.html', 'w') { |f| f.puts result } unless result.empty?
end
