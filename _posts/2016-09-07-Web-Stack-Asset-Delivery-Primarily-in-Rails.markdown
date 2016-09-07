---
layout: post
title:  "Web Stack Asset Delivery, Primarily in Rails"
date:   2016-09-07 18:20:29 -0400
categories: rails assets pipeline
---
Rails 4 uses the asset pipeline to precompile all assets into a respect minimized copy and save that to the public/assets directory, in production. This can create either or both, compressed or inflated assets. Those generally are stored in the public directory that is created. All the assets for your site are going to be store there. More about the asset pipeline can be read here:

* http://guides.rubyonrails.org/asset_pipeline.html#precompiling-assets
* https://launchschool.com/blog/rails-asset-pipeline-best-practices

Browser -> web server -> app server (puma, unicorn, physio, etc.) -> rails

So by default, if a developer were to be using another web server like NGINX or Apache, it would be smart enough to serve either a compressed copy of the assets or not. That means when a request desires the asset, these web servers will be smart enough to serve the assets from that created public directory, without having to ask rails to serve the static assets. By default. Therefore it saves time and process power from the app server and rails.

When a user requests assets, the web server can determine if the user's web browser is able to process either compressed or inflated assets. Whether it's gzip or any other supported compression, if the web browser can handle it, then the web server will prioritize the compressed asset for speed and size.

Browser -> web server -> app server (puma, unicorn, physio, etc.) -> rails

In the case of using a web server host such as Heroku, they are one of the exceptions. Since Heroku does not like to serve compressed assets from the public directory, that rails created, it will serve the inflated ones first. In that case, even though the asset has both types, it will only ever pick the inflated ones.

The process is the same as any other web stack. However, Heroku used to insert plugins during your app deploy to help accommodate this functionality but has since removed that. In that case, there are gems that you can add to your rails application that will provide the same functionality as those plugins. If you manage to successfully configure one of those gems or rack middleware plugin, such as Rack::Deflate, it will allow Heroku and Rails to serve those compressed assets, prioritized over inflated assets. After all that, you're back to square one like every other configured web stack just only in this case, Rails has to do the heavy lifting of providing those compressed assets instead which adds processing time. More about rails having to serve static assets:

* https://github.com/eliotsykes/rack-zippy/blob/v3.0.1/README.md
* http://berislavbabic.com/speed-up-rails-on-heroku-by-using-rackzippy/
* https://github.com/heroku/rails_12factor/blob/master/README.m

To help alleviate processing time on your server as well as speeding up the experience for the end user, developers tend to sway towards the recommended best practice of using a CDN. CDNs allows a website to serve cached static assets to the end user but delivering them from the closet geographical location to decrease latency. This case, the infrastructure of the CDN is a type of cloud based system. Since it's a cloud based system, the end user does not care from where they receive their information but as long as they receive it in a timely manner. It's up to the provider to determine from where this information be delivered as long as it follows the company's desired specification, I.e. Speed and decreased latency. For instance, if a user lives in Australia and happens to visit a website that is served from America, the user will request to download all necessary website files. However since the user lives quite far from the geographical location to where the website is being served, the latency is increased.

To help alleviate this situation, the website developer could implement a CDN in their application, so in this case a rails application. What this is going to do is when the rails application is deployed, it's going to set the source of each asset to the URL location of the asset on that CDN. This includes anything that is configured. If the end user requests an asset that is on the CDN, instead of asking rails, the web browser asks the CDN and the CDN will serve the static asset from the closest geographical location to the end user.

Browser -> CDN -> web server -> app server (puma, unicorn, physio, etc.) -> rails

Based on this rough diagram, the CDN is out front so any asset that is requested, if it's been cached by the CDN, then the asset is returned by the CDN and never has to ever follow the chain to the rails application. However there is a catch to a CDN.

A CDN is only as powerful as the files it caches and for how long. As for files it caches, the primary point, in my opinion is the ability to quickly serve up static assets. If the CDN has the image stored on the closest geographical location, then the asset is served quickly. If it's not stored then unfortunately for this user, their request will have to go all the way to the webserver or rails application to receive that asset. However once that asset is returned from the web server or rails application, that asset is then stored on the CDN so any future request, regardless of user, can take advantage of the short latency. In other words, if there is a Cache-Miss on any asset on the CDN then it's redirected to the webserver or rails application. This idea also brings up an interesting functionality. The more users in a geographical area visit that site, the more frequent, abundant, and current assets are going to cached and served from the closest geographical CDN server to each end user of that website! So that means there can be a chance where a single user could visit the website instantly and have the most up to date information where as one unlucky fellow could have to wait just a second longer to benefit the rest of the user population.

Depending on how long those cached files are going to be saved, certain assets may benefit or harm the user experience. For assets that are infrequent to change, such as a company logo, they can be told by the developer to the CDN to store them for a longer period of time. For all assets, they are cached for a period of time. Once their expiration date is up, then the cached file is dumped and the next request will have to ask the webserver or rails application for the current asset. If you cache too long, then the files served may not contain current information. It may be fast but the information could be ages old. If you cache too short, then you aren't utilizing the functionality of a CDN. In that case, in every request, majority of them would end up asking the webserver or rails application for the asset therfore your latency will increase. So there has to be a balance and some well planned decision making by the developers to know when is the right time to expire cached files.