
# Readme myself

## latest running commands .. 
- 2023.03.01 
  - after updated ubuntu 22.04, I need some error resolving steps and finanlly run the server using `bundle exec jekyll serve` command.
## Tips 
- search 가 latest 를 반영하기 위해서는 _site/search.json 를 업데이트해야 함 (컨텐츠 자체는 자동업데이트 되므로, _site 를 add and push만 하면 됨 )

## Update logs 

## Build issues
### 2022.07.16
- m1 macbook pro 에 작업환경을 구축하고자 함 
- https://choijaegwon.github.io/githubblog/GithubBlog1/ 를 참고하였음 
- 설치 이후 bundle exec jekyll serve 해주면 됨. 

### 2022.07.10
- 새로운 우분투에서 `$ jekyll s` 실행하려니 아래와 같은 에러가 나서 
    ```
    Traceback (most recent call last):
	    13: from /usr/bin/jekyll:9:in `<main>'
	    12: from /usr/lib/ruby/vendor_ruby/jekyll/plugin_manager.rb:50:in `require_from_bundler'
	    11: from /usr/lib/ruby/2.7.0/bundler.rb:149:in `setup'
	    10: from /usr/lib/ruby/2.7.0/bundler/runtime.rb:20:in `setup'
	     9: from /usr/lib/ruby/2.7.0/bundler/runtime.rb:101:in `block in definition_method'
	     8: from /usr/lib/ruby/2.7.0/bundler/definition.rb:226:in `requested_specs'
	     7: from /usr/lib/ruby/2.7.0/bundler/definition.rb:237:in `specs_for'
	     6: from /usr/lib/ruby/2.7.0/bundler/definition.rb:170:in `specs'
	     5: from /usr/lib/ruby/2.7.0/bundler/definition.rb:258:in `resolve'
	     4: from /usr/lib/ruby/2.7.0/bundler/resolver.rb:22:in `resolve'
	     3: from /usr/lib/ruby/2.7.0/bundler/resolver.rb:49:in `start'
	     2: from /usr/lib/ruby/2.7.0/bundler/resolver.rb:258:in `verify_gemfile_dependencies_are_found!'
	     1: from /usr/lib/ruby/2.7.0/bundler/resolver.rb:258:in `each'
    /usr/lib/ruby/2.7.0/bundler/resolver.rb:290:in `block in verify_gemfile_dependencies_are_found!': Could not find gem 'rake (~> 12.0)' in any of the gem sources listed in your Gemfile. (Bundler::GemNotFound) 
    ```
- `gem install rake && bundle install` 해서 solve하였음. 

# Customizing ..
- modify `_config.yml` and do `$ sudo gem install plainwhite`
  - then, `$ jekyll s` to visualize the updated version at local host. 

# plainwhite

Simplistic jekyll portfolio-style theme for writers.

**Demo**: [samarsault.com](https://samarsault.com)

![plainwhite theme preview](/screenshot.png)

## Installation on Github Pages

Add this line to your site's `_config.yml`:

```yaml
remote_theme: samarsault/plainwhite-jekyll
```

## Installation

Add this line to your Jekyll site's `Gemfile`:

```ruby
gem "plainwhite"
```

And add this line to your Jekyll site's `_config.yml`:

```yaml
theme: plainwhite
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install plainwhite

## Usage

The "plainwhite" key in \_config.yml is used to customize the theme data.

```yaml
plainwhite:
  name: Adam Denisov
  tagline: Developer. Designer
  date_format: "%b %-d, %Y"

  social_links:
    twitter: samarsault
    github: samarsault
    linkedIn: in/samarsault # format: locale/username
```

**Updating Placeholder Image**

The placeholder portfolio image can be replaced by the desired image by placing it as `assets/portfolio.png` in your jekyll website, or by changing the following line in `_config.yaml`

```yaml
plainwhite:
  portfolio_image:  "assets/portfolio.png" # the path from the base directory of the site to the image to display (no / at the start)
```

To use a different image for dark mode, e.g. with different colors that work better in dark mode, add a `portfolio_image_dark` entry in addition to the `portfolio_image`.

```yaml
plainwhite:
  portfolio_image:      "assets/portfolio.png"
  portfolio_image_dark: "assets/portfolio_dark.png"
```

**Comments (Disqus)**

Comments on posts can be enabled by specifying your disqus_shortname under plainwhite in `_config.yml`. For example,

```yaml
plainwhite:
  disqus_shortname: games
```

**Google Analytics**

It can be enabled by specifying your analytics id under plainwhite in `_config.yml`

```yaml
plainwhite:
  analytics_id: "< YOUR ID >"
```

**Sitemap**

It can be toggled by the following line to under plainwhite in `_config.yml`

```yaml
plainwhite:
  sitemap: true
```

**Excerpts**

Excerpts can be enabled by adding the following line to your `_config.yml`

```yaml
show_excerpts: true
```

**Layouts**

- Home
- Page
- Post

**Navigation**

Navigation can be enabled by adding the following line to your `_config.yml`

```yaml
plainwhite:
  navigation:
    - title: My Work
      url: "/my-work"
    - title: Resume
      url: "/resume"
```

**Mobile**

By default, Plainwhite places the sidebar (logo, name, tagline etc.) above the content on mobile (narrow screens).
To condense it (moving some things to the bottom of the page and making the rest smaller) so it takes up less space, add the following to your `_config.yml`:

```yaml
plainwhite:
  condensed_mobile:
    - home
    - post
    - page
```

This chooses which layouts (types of page) should be condensed on mobile screens. E.g. if you want everything but the landing page to be condensed, remove `home` from the list. This option does not affect rendering on wider screens.

**Dark mode**

Dark mode can be enabled by setting the `dark_mode` flag in your `_config.yml`

The website will check the OS preferred color scheme and set the theme accordingly, the preference will then be saved in a cookie

```yaml
plainwhite:
  dark_mode: true
```

![plainwhite dark theme previe](/dark.png)

**Multiline tagline**

Tagline can be multiline in this way

```yaml
plainwhite:
  tagline: |
  First Line. 

  Second Line. 

  Third Line.
```

**Search-bar**

Search-bar can be enabled by adding the following line to `config.yml`

```yaml
plainwhite:
  search: true
```

Search is powered by [Simple-Jekyll-Search](https://github.com/christian-fei/Simple-Jekyll-Search) Jekyll plugin. A `search.json` containing post meta and contents will be generated in site root folder. Plugin JavaScript will then match for posts based on user input. More info and `search.json` customization documentation can be found in plugin repository.

**Base URL**

You can specify a custom base URL (eg. example.com/blog/) by adding the following line to `_config.yaml`. Note that there is no trailing slash on the URL.

```yaml
baseurl: "/blog"
```

**Language**

You can set the `lang` attribute of the `<html>` tag on your pages by changing the following line in `_config.yml`:

```yaml
plainwhite:
  html_lang: "en"
```

[See here for a full list of available language codes](https://www.w3schools.com/tags/ref_country_codes.asp)

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/samarsault/plainwhite-jekyll. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## Development

To set up your environment to develop this theme, run `bundle install`.

Your theme is setup just like a normal Jekyll site! To test your theme, run `bundle exec jekyll serve` and open your browser at `http://localhost:4000`. This starts a Jekyll server using your theme. Add pages, documents, data, etc. like normal to test your theme's contents. As you make modifications to your theme and to your content, your site will regenerate and you should see the changes in the browser after a refresh, just like normal.

When your theme is released, only the files in `_layouts`, `_includes`, `_sass` and `assets` tracked with Git will be bundled.
To add a custom directory to your theme-gem, please edit the regexp in `plainwhite.gemspec` accordingly.

## Donation
If this project help you reduce time to develop, you can give me a cup of coffee :) 

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://paypal.me/thelehhman)

## License

The theme is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## More themes

- [Texture](https://github.com/samarsault/texture)
