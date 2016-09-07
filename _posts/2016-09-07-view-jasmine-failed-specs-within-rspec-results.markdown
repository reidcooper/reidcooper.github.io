---
layout: post
title:  "View Failed Jasmine Specs within Rspec Results"
date:   2016-09-07 18:20:29 -0400
categories: rails jasmine specs
---
During my time at Mack Weldon, we've been using Rspec for our Rails testing framework as well as Jasmine for our frontend Javascript testing.
In order to help streamline testing, we use Semaphore to help test our code commits off of github. The only problem with this process is that Semaphore will only tell us if our Rspec specs failed but will not tell us which of our Jasmine specs failed.
In our Rspec suite, we just quickly wrote up a simple spec that will access the Jasmine suite spec page on our app and monitor if any failed spec DOM elements show up; if any do then we just throw a failure in our Rspec tests. The only problem is what I describe previously.
This fix, I finally was able to come up with a work around that should enable us to see which Jasmine specs failed within the results of our Rspec spec results.

The primary focus of this article is the code within the `before(:each)` block. I just execute the script within Poltergeist and out comes which specs, if any, that failed.

{% highlight ruby %}
require 'spec_helper'

describe "the jasmine suite", :js => true do
  # this is the rspec hook into the jasmine suite
  # do not remove

  before(:each) do
    FactoryGirl.create(:active_home_page)
  end

  after(:each) do
    failure_script = 'console.log("\n\n"+$("span.jasmine-bar.jasmine-failed").text());
                        console.log("Failures:");
                        $(".jasmine-spec-detail.jasmine-failed").each(function(index, error_group){
                          console.log("\t"+$(error_group).find(".jasmine-description").text());
                          $(error_group).each(function(index, error) {
                            $(error).find(".jasmine-messages").find(".jasmine-result-message").each(function(index, message){
                              console.log("\t\t"+$(message).text());
                            });
                          });
                        });'
    page.execute_script(failure_script)
  end

  it "passes" do
    visit '/specs'

    page.should have_css('span.jasmine-bar.jasmine-passed')
    page.should have_content('0 failures')
  end
end
{% endhighlight %}

If this post is never updated, please refer to this gist: https://gist.github.com/reidcooper/f92e75d46f2cd4322c87af1c6b58d940