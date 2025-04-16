require 'html-proofer'
require 'nokogiri'
require 'open-uri'

desc "Clean up the site directory"
task :clean do
  sh("rm -rf _site")
end

desc "build"
task :build => :clean do
  sh("bundle exec jekyll build")
end


def should_not_run_external_url_checks?
  if ENV['CI']
    false
  else
    ENV['RUN_EXTERNAL_CHECKS'].nil? || ENV['RUN_EXTERNAL_CHECKS'] == 'false'
  end
end

desc "Validate all links"
task validate_broken_links: [:build] do
  options = {
      :disable_external     => should_not_run_external_url_checks?,
      :ignore_urls          => [],
      :allow_hash_href      => false,
      :allow_missing_href   => true,
      :check_external_hash  => false,
      :href_ignore          => ['/https:\/\/www\.youtube\.com\/.*/'],
      :validation           => {
          :report_invalid_tags  => false,
          :report_script_embeds => false,
          :report_missing_names => false ,
      },
      :typhoeus => {
          :followlocation => true,
          :connecttimeout => 500,
      },
      :ignore_missing_alt   => true,
      :log_level            => :info,
  }

  STDERR.puts "WARNING: Not checking outbound links. Set environment variable: " +
                  "RUN_EXTERNAL_CHECKS to 'true' to run them" if should_not_run_external_url_checks?

  puts "\nRunning link checks, html format and verifying that it can be hosted in a subdirectory (relative links):"

  HTMLProofer.check_directory('_site', options).run
end

desc "Validate unused resources"
task validate_unused_resources: [:build] do
  rm_rf("_crawl_site")
  sh("pkill -f jekyll") do |ok, res|
    if !ok
      puts "Jekyll process not running"
    else
      puts "Jekyll process killed"
    end
  end

  sh("bundle exec jekyll serve --port 9953 --detach")
  sleep(5)


  urls = urls_in_sitemap("http://127.0.0.1:9953/sitemap.xml")
  p urls

  sh("wget --no-verbose --recursive --directory-prefix=_crawl_site --no-host-directories #{urls.join(' ')}") do |ok, res|
    if !ok
      puts "Wget failed, ignoring"
    end
  end
  sh("pkill -f jekyll")
  sh("diff -rq _crawl_site _site --ignore-matching-lines='data-tab-content-selector' --ignore-matching-lines='data-tab-content-id' --exclude=assets --exclude=sitemap.xml --exclude=documentation.html --exclude=specmatic-logo.png")
end

desc "Perform all checks"
task :check => [:validate_broken_links, :validate_unused_resources]

def urls_in_sitemap(sitemap_url)
  sitemap_content = URI.open(sitemap_url).read
  sitemap = Nokogiri::XML(sitemap_content)
  sitemap.remove_namespaces!
  return sitemap.xpath("//url/loc").map(&:text)
end
