task :swig do
  run("swig -DLIBMEMCACHED_WITH_SASL_SUPPORT -Iext/libmemcached-0.32 -ruby -autorename -o ext/rlibmemcached_wrap.c.in ext/rlibmemcached.i", "Running SWIG")
  swig_patches = {
    "#ifndef RUBY_INIT_STACK" => "#ifdef __NEVER__" # Patching SWIG output for JRuby.
  }.map{|pair| "s/#{pair.join('/')}/"}.join(';')
  # sed has different syntax for inplace switch in BSD and GNU version, so using intermediate file
  run("sed '#{swig_patches}' ext/rlibmemcached_wrap.c.in > ext/rlibmemcached_wrap.c", "Apply patches to SWIG output")
end

task :exceptions do
  $LOAD_PATH << "lib"
  require 'memcached'
  Memcached.constants.sort.each do |const_name|
    const = Memcached.send(:const_get, const_name)
    next if const == Memcached::Success or const == Memcached::Stored
    if const.is_a? Class and const < Memcached::Error
      puts "* Memcached::#{const_name}"
    end
  end
end

task :benchmark do
 exec("ruby #{File.dirname(__FILE__)}/test/profile/benchmark.rb")
end

task :rb_profile do
 exec("ruby #{File.dirname(__FILE__)}/test/profile/rb_profiler.rb")
end

task :c_profile do
 exec("ruby #{File.dirname(__FILE__)}/test/profile/c_profiler.rb")
end

task :valgrind do
 exec("ruby #{File.dirname(__FILE__)}/test/profile/valgrind.rb")
end

task :prerelease => [:manifest, :test, :install]

def run(cmd, reason)
  puts reason
  puts cmd
  raise "'#{cmd}' failed" unless system(cmd)
end
