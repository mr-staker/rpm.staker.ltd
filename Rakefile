repo = 'staker-rpm'

desc 'Prune package from repository'
task :prune do
  if ENV['pkg'].nil?
    warn 'ERR: must specify which pkg to prune'
    Kernel.exit 1
  end

  sh "repo-mgr remove-pkg --repo #{repo} --path #{ENV['pkg']}"
  sh "bfg --delete-files #{ENV['pkg']}"

  Rake::Task[:clean].invoke
end

desc 'Alias for prune task'
task purge: %i[prune]

desc 'Garbage collect git repository'
task :gc do
  sh 'git reflog expire --expire=now --all && git gc --prune=now --aggressive'

  Rake::Task[:clean].invoke
end

desc 'Clean reports'
task :clean do
  repo = File.basename(__dir__)

  Dir.chdir('..') do
    rm_rf "#{repo}.bfg-report"
  end
end

desc 'Force push changes'
task :push do
  sh 'git push --force'
end

desc 'List packages in repository'
task :list do
  sh "repo-mgr list-pkgs --repo #{repo}"
end

desc 'List actual files in git repository'
task :files do
  cmd = 'git verify-pack -v .git/objects/pack/*.idx | grep blob | '\
    "awk '{print $1}'"
  blobs = `#{cmd}`.split("\n")

  blobs.each do |blob|
    file = `git rev-list --objects --all | grep "#{blob}" | cut -d" "  -f2-`
    puts "#{blob} => #{file}"
  end
end

desc 'Clean distribution files'
task :distclean do
  %w[
    Contents-amd64.gz InRelease Release* Packages* *-primary.xml.gz
    *-filelists.xml.gz *-filelists.xml.zck *-other.sqlite.bz2
    *-primary.sqlite.bz2 *-filelists.sqlite.bz2 *-other.xml.gz
    *-other.xml.zck *-primary.xml.zck repomd.xml*
  ].each do |file|
    sh "bfg --delete-files #{file}"
  end

  sh 'bfg --delete-files *.deb'
  sh 'bfg --delete-files *.rpm'
end

desc 'Print size of git repo'
task :size do
  sh 'du -sh .git'
end

desc 'Export repo'
task :export do
  sh "repo-mgr export --repo #{repo}"
end
