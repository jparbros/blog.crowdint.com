---
layout: post
title: Acceptance testing with Steak
author: Ana Rosas
email: ana@crowdint.com
avatar: 2f2bc78de007915554f49c3ea0fef125
short_date: Jun 30
---

Acceptance testing is a technique that helps determine if the requirements of
our application are met. There are several frameworks to help us with this.
One of the most popular is Cucumber, in which you practically describe in plain English
your stories, but you also have to code a lot of steps in order for your tests to work.

To avoid this, and just test your features in Ruby, we have Steak,
which works very well with Rspec.
To use it you only need to put this on your Gemfile:

{% highlight ruby %}
group :development, :test do
  gem "steak"
end
{% endhighlight %}

Then run the bundle install command and after that install steak

{% highlight bash %}
$ bundle install
$ rails g steak:install
{% endhighlight %}

Steak will create the acceptance folder inside your specs directory. This is where
all your acceptance tests will be placed.
Now, to create your test, you need to run the command

{% highlight bash %}
$ rails g steak:spec my_first_steak_test
{% endhighlight %}

This will create a file with the basic structure of your test, and now you are
ready to start coding!

{% highlight ruby %}
require 'acceptance/acceptance_helper'

feature 'Steak feature', %q{
  In order to ...
  As a ...
  I want ...
} do

  scenario 'first scenario' do
    true.should == true
  end

end
{% endhighlight %}

As you can see it starts pretty much as a Cucumber feature, where you can describe
what your feature is about and what should every scenario do.

Here's a small feature to test a form in an application.

{% highlight ruby %}
require File.expand_path(File.dirname(__FILE__) + '/acceptance_helper')

feature "Add Products to Store", %q{
  In order to add products in my store
  As an admin
  I want to create a product
} do

  background do
    login_user!('admin@example.com', 'password')
  end

  scenario "Add products to store" do
    visit new_product_path
    
    page.should have_css('form')
    fill_the_following(
      'Name' => 'New Product',
      'Description' => 'Fantastic product',
      'Price' => '32.00',
    )
    
    click_link_or_button 'Create Product'

    page.should have_css('div', :text => 'Your product has been created')
  end
end
{% endhighlight %}

As you can see the syntax is pretty easy and looks a lot like Rspec. The cool thing
is that Steak creates a support folder inside the acceptance directory, where you
have your helpers.rb file and the paths.rb file.

In the helpers.rb file you can put all the methods you need to run your test,
methods that you might end up using a lot among all your specs, like the one in the
example login_user!

{% highlight ruby %}
def login_user!(email, password)
  visit homepage
  click_link_or_button 'Log In'

  fill_the_following(
    'Email' => email,
    'Password' => password
  )

  click_link_or_button 'Log In'
end
{% endhighlight %}

For paths, you can put directly the route like in the example, or you can define your
own named routes in the paths.rb file, like

{% highlight ruby %}
def create_product
  "/products/new"
end
{% endhighlight %}

Hope this little guide will help you as an introduction to the Steak world!
Which in my opinion is not difficult nor totally different from Cucumber, I think
its even easier, because it comes with a lot of helpers and methods to check the
styles and contents in your page.

The other thing I like about Steak, as I mentioned at the beginning of this post, is
that you get rid of tons of steps definitions, you just write down methods for
the repeated steps you take in all of your tests.

Thanks for reading and happy testing!
