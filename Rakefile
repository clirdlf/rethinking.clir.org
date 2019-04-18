require 'chronic'
require 'fileutils'
require 'mechanize'
require 'net/http'
require 'open-uri'
require 'safe_yaml'

@agent = Mechanize.new

def make_filename(date, title)
  formatted_date = date.strftime('%Y-%m-%d')
  post_name = title.split(%r{ |!|/|:|&|-|$|,|“|”}).map do |i|
    i.downcase if i != ''
  end.compact.join('-')

  "#{formatted_date}-#{post_name}.html"
end

def clean_date(date)
  date.search('.//span').remove
  date.search('.//a').remove
  Chronic.parse(date.text.strip)
end

def parse_meta(data)
  {
    'layout' => 'post',
    'title' => data.css('#PageTitleH1 > text()').text.strip,
    'author' => data.css('#MainCopy_ctl04_ucPermission_ByLinePanel a').text,
    'date' => clean_date(data.css('#MainCopy_ctl04_ucPermission_ByLinePanel'))
    # content: data.css('.blogs-block')
  }
end

def clean_content(content)
  content.css('.form-inline').remove
  content.css('#MainCopy_ctl04_ucPermission_BlogItemRating_CommentPanel').remove
  content.css('#MainCopy_ctl04_ucPermission_dvStatsContainer').remove
  content
end

def download_image(url, dest)
  open(url) do |u|
    File.open(dest, 'wb') { |f| f.write(u.read) }
  end
end

def get_images(content)
  images = content.search('//img')

  puts 'Analyzing images:'

  images.each do |img|
    uri = URI.parse(img['src'])

    next if File.basename(uri.path) == ''

    local_file = "./assets/images/#{File.basename(uri.path)}"
    download_image(uri, local_file)
    puts "Downloading #{uri}"
  end
end

namespace :fix do
  desc 'Fix image links'
  task :image_links do
  end
end


namespace :import do


  desc 'Import HigherLogic posts'
  task :posts do
    f = File.open('lib/blog_links.txt', 'r')

    f.each_line do |url|
      # url = 'https://connect.clir.org/blogs/jodi-reeves-eyre/2017/09/14/torch-bearers'
      @content = @agent.get(url)
      @metadata = parse_meta(@content)

      filename = make_filename(@metadata['date'], @metadata['title'])

      FileUtils.mkdir_p('_posts')

      cleaned_content = clean_content(@content.css('.blogs-block'))

      get_images(cleaned_content)

      File.open("_posts/#{filename}", 'w') do |f|
        puts "Importing #{filename}"
        f.puts @metadata.to_yaml
        f.puts "---\n\n"
        f.puts cleaned_content
      end
    end

    f.close
  end
end
