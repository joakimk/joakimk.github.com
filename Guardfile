guard 'livereload' do
  watch(%r{_site/index.html$})
end

guard :shell do
  watch(/_posts\/(.*).markdown/) {|m| `rake build` }
  watch(/_layouts\/(.*)/) {|m| `rake build` }
end
