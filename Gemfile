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
end
