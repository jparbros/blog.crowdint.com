---
layout: post
title: Cool date formatting in Rails
author: Gonzalo Fernandez
email: gonzalo@crowdint.com
avatar: d3177da7794ac3ce603d65b33bf4a981
---

In almost every Rails application, you will need to show dates in a given format...
That's why the strftime function exists, and you can customize the output to meet your needs to something like this:

{% highlight ruby %}

2.days.ago.strftime('%m/%d/%Y %l:%M %P')
=> "12/19/2010  4:18 pm"

{% endhighlight %}

This is very customizable, but is hard to mantain and definitly... not cool ;)
In fact, this looks like old C/PHP code


## The "cool" way

Rails is all cool, and DRY and following conventions, so that's why we need to use the to__s method, which uses the Time::DATE_FORMATS...

By default, Rails comes with a few one you can use:

- :long_ordinal
- :long
- :db
- :short
- :time
- :number
- :rfc822

So, you can execute for example:

{% highlight ruby %}

2.days.ago.to_s :short
=> "19 Dec 16:27"

{% endhighlight %}

Much better now, isn't it?


## Custom formats

The problem is that Rails doesn't provide an obvious way to do that...

So reading the Rails code (activesupport/lib/active_support/core_ext/date_time/conversions.rb) I found that you could create an initializer in your Rails project and add your own custom formats there:

config/initizers/time_formats.rb
{% highlight ruby %}

# == Adding your own datetime formats to to_formatted_s
# DateTime formats are shared with Time. You can add your own to the
# Time::DATE_FORMATS hash. Use the format name as the hash key and
# either a strftime string or Proc instance that takes a time or
# datetime argument as the value.

Time::DATE_FORMATS[:month_and_year] = "%B %Y"
Time::DATE_FORMATS[:human] = '%m/%d/%y @ %I:%M%p'

{% endhighlight %}

So here I'm creating 2 new date/time formats to use with the to_s method.

Let's see how different and clean the code is:

{% highlight ruby %}

# the "cool" way
2.days.ago.to_s :human

# the old school way
2.days.ago.strftime '%m/%d/%y @ %I:%M%p'

{% endhighlight %}

Cool, isn't it?

And after several reuses of the format, you will start noticing the adventages... especially when you need a change in the format With this aproach is only one change (versus search the whole project for strftime usages).

Hope this help...
Thanks for reading
Gonzalo "aka Chalo" Fernandez

