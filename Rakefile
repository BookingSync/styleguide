require 'fileutils'

desc "Remove the output dir"
task :remove_output_dir do
  # FIXME: Add again once grunt is ran from the "publish" task
  # FileUtils.rm_r('_gh_pages') if File.exist?('_gh_pages')
end

def commit_message
  publish_emojis = [':boom:', ':rocket:', ':metal:', ':bulb:', ':zap:',
    ':sailboat:', ':gift:', ':ship:', ':shipit:', ':sparkles:', ':rainbow:']
  default_message = "P U B L I S H #{publish_emojis.sample}"

  print "Enter a commit message (default: '#{default_message}'): "
  STDOUT.flush
  mesg = STDIN.gets.chomp.strip

  mesg = default_message if mesg == ''
  mesg.gsub(/'/, '') # Allow this to be handed off via -m '#{message}'
end

desc "Publish to http://styleguide.bookingsync.com"
task :publish => [:remove_output_dir] do
  mesg = commit_message

  # FIXME: We should run grunt before publishing to ensure that we push the latest content
  # sh "grunt"

  ENV['GIT_DIR'] = File.expand_path(`git rev-parse --git-dir`.chomp)
  old_sha = `git rev-parse refs/remotes/origin/gh-pages`.chomp
  Dir.chdir('_gh_pages') do
    ENV['GIT_INDEX_FILE'] = gif = '/tmp/dev.gh.i'
    ENV['GIT_WORK_TREE'] = Dir.pwd
    File.unlink(gif) if File.file?(gif)
    `git add -A`
    tsha = `git write-tree`.strip
    puts "Created tree   #{tsha}"
    if old_sha.size == 40
      csha = `git commit-tree #{tsha} -p #{old_sha} -m '#{mesg}'`.strip
    else
      csha = `git commit-tree #{tsha} -m '#{mesg}'`.strip
    end
    puts "Created commit #{csha}"
    puts `git show #{csha} --stat`
    puts "Updating gh-pages from #{old_sha}"
    `git update-ref refs/heads/gh-pages #{csha}`
    `git push origin gh-pages`
  end
end
