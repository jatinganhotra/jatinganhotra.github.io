# frozen_string_literal: true

source "https://rubygems.org"
gemspec

gem "jekyll", ENV["JEKYLL_VERSION"] if ENV["JEKYLL_VERSION"]
gem "kramdown-parser-gfm" if ENV["JEKYLL_VERSION"] == "~> 3.9"
gem 'jekyll-gist'
gem 'jekyll-sitemap'

group :jekyll_plugins do
    gem 'jekyll_quote'
    gem "jekyll-seo-tag"
    gem 'jekyll-spaceship'
    # https://lazee.github.io/premonition-demo/
    # https://github.com/lazee/premonition/tree/master
    gem 'premonition', '~> 4.0', '>= 4.0.2'
    # https://github.com/bdesham/reading_time?tab=readme-ov-file
    gem 'liquid_reading_time'
end
