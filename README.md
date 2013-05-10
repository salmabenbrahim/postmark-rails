# postmark-rails gem

[![Build Status](https://travis-ci.org/wildbit/postmark-rails.png?branch=master)](https://travis-ci.org/wildbit/postmark-rails) [![Code Climate](https://codeclimate.com/github/wildbit/postmark-rails.png)](https://codeclimate.com/github/wildbit/postmark-rails)

The Postmark Rails Gem is a drop-in plug-in for ActionMailer to send emails via [Postmark](https://postmarkapp.com), an email delivery service for web apps. The gem has been created for fast implementation and fully supports all of [Postmark’s features](https://postmarkapp.com/why-postmark).

## Supported Rails Versions

* 4.0
* 3.x
* 2.3.x

## Configuring your Rails application

### Rails 3 and above

Add this to your Gemfile: (change version numbers if needed)

``` ruby
gem 'postmark-rails', '~> 0.5.0'
```

Don’t forget to run `bundle install` command every time you change something in the Gemfile.

Add this to your config/application.rb:

``` ruby
config.action_mailer.delivery_method = :postmark
config.action_mailer.postmark_settings = { :api_key => "your-api-key" }
```

### Rails 2.x

Add this to the config/environment.rb file:

``` ruby
Rails::Initializer.run do |config|

  # ...

  config.gem 'postmark-rails'
  require 'postmark-rails'

  config.action_mailer.postmark_api_key = "your-api-key"
  config.action_mailer.delivery_method = :postmark

  # ...

end
```

For the API details, refer to the [developer documentation](http://developer.postmarkapp.com).

## Tagging your deliveries

You can use tags to categorize outgoing messages and attach application-specific information. Tagging the different types of email that you send lets you [review statistics and bounce reports separately](http://developer.postmarkapp.com/developer-build.html#message-format).

### Rails 3 and above

``` ruby
class TestMailer < ActionMailer::Base

  def tagged_message
    mail(
      :subject => 'hello',
      :to      => 'sheldon@bigbangtheory.com',
      :from    => 'leonard@bigbangtheory.com',
      :tag     => 'my-tag'
    )
  end

end
```

### Rails 2

``` ruby
class SuperMailer < ActionMailer::Base

  def email
    from       "no-reply@example.com"
    subject    "Some marvelous email message"
    recipients "someone-fancy@example.com"
    tag        "my-another-tag"
  end

end
```

## Sending attachments

You can also send file attachments with Postmark. Read our Developer docs for [additional information](http://developer.postmarkapp.com/developer-build.html#attachments).

### Rails 3 and above

The Postmark gem is compatible with [ActionMailer attachments API](http://api.rubyonrails.org/classes/ActionMailer/Base.html#method-i-attachments). It allows you to specify the name, content-type and other attributes for your attachments.

The legacy `:postmark_attachments` attribute is *no longer supported* on Rails 3.2.13 and above.


``` ruby
class TestMailer < ActionMailer::Base

  def message_with_attachment
    attachment['42.jpg'] = File.read("/path/to/file")
    mail(
      :subject              => 'hello',
      :to                   => 'sheldon@bigbangtheory.com',
      :from                 => 'leonard@bigbangtheory.com'
    )
  end

end
```

### Rails 2

``` ruby
class SuperMailer < ActionMailer::Base

  def email
    from                 "no-reply@example.com"
    subject              "Some marvelous email message"
    recipients           "someone-fancy@example.com"
    postmark_attachments [File.open("/path/to/file")]
  end

end
```

You can pass either an array of File objects or a single object. Postmark will detect the file name automatically and send an attachment with the "application/octet-stream" content type. If you want more control on how attachments get formatted, you can pass Hash objects, which contain the custom settings such as file name or content-type. Here is an example:

``` ruby
#
# Don't forget to read your file and base64-encode it,
# before assigning it to "Content".
#
message.postmark_attachments = {
  "Name"        => "fancy-file-name.jpg",
  "Content"     => [ IO.read("path/to/file") ].pack("m"),
  "ContentType" => "image/jpeg"
}
```

## Sending in batches

While Postmark is focused on transactional email, we understand that developers
with higher volumes or processing time constraints need to send their messages
in batches. To facilitate this we provide a batching endpoint that permits you
to send up to 500 well-formed Postmark messages in a single API call.

``` ruby
client = Postmark::ApiClient.new('your-api-key')

messages = []
messages << DigestMailer.weekly_digest(@user1)
messages << DigestMailer.weekly_digest(@user2)

client.deliver_messages(messages)

messages.first.delivered?
# => true

messages.all?(&:delivered)
# => true
```

## Additional information

Looking for the advanced usage examples? Check out [the documentation](https://github.com/wildbit/postmark-gem/blob/master/README.md) for the `postmark` gem. The `postmark-rails` gem is built on top of it, so you can benefit from all its features.

## Requirements

* `postmark` gem version 1.0 and higher is required.
* You will also need a Postmark account, a server and at least one sender signature set up to use it. To get an account, [sign up](https://postmarkapp.com/sign_up)!.


## Note on Patches/Pull Requests

* Fork the project.
* Make your feature addition or bug fix.
* Add tests for it. This is important so we don’t break it in a future version unintentionally.
* Commit, do not mess with rakefile, version, or history.
* Send a pull request. Bonus points for topic branches.

## Authors & Contributors

* Artem Chistyakov
* Petyo Ivanov
* Ilya Sabanin
* Hristo Deshev
* Randy Schmidt
* Chris Williams
* Nicolás Sanguinetti
* Laust Rud Jacobsen (rud)

## Copyright

Copyright © 2010—2013 Wildbit LLC. See LICENSE for details.
