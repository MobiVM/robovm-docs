require 'asciidoctor'
require 'erb'
require 'fileutils'

src_folder = 'src/main/asciidoc'

guard 'shell' do
  watch(/^#{src_folder}\/.*\.adoc$/) {|m|
    path = m[0][src_folder.length+1..-1]
    path = path.sub(/\.adoc$/, '.html')

    Asciidoctor.render_file("#{src_folder}/index.adoc", 
      {
        :to_file => "target/live/index.html", 
        :mkdirs => true, 
        :safe => 0,
        :attributes => { "stylesheet" => "../../../stylesheets/foundation.css" }
      })
  }
  watch(/^#{src_folder}\/.*\.png$/) {|m|
    path = m[0][src_folder.length+1..-1]
    FileUtils.mkdir_p(File.dirname("target/live/#{path}"))
    FileUtils.cp(m[0], "target/live/#{path}")
  }
end
