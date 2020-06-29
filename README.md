# Diffcrypt

[![Gem Version](https://badge.fury.io/rb/diffcrypt.svg)](https://rubygems.org/gems/diffcrypt)
[![CircleCI](https://circleci.com/gh/marcqualie/diffcrypt.svg?style=svg)](https://circleci.com/gh/marcqualie/diffcrypt)


Diffable encrypted files that you can safely commit into your repo.



## Installation

Add this line to your application's Gemfile:

```ruby
gem 'diffcrypt'
```

And then execute:

    $ bundle install

Or install it globally (to use the CLI from any project):

    $ gem install diffcrypt



## Usage

There are a few ways to use the library, depending on how advanced your use case is.


### CLI

The easiest way to get started is to use the CLI.

```shell
diffcrypt decrypt -k $(cat test/fixtures/master.key) test/fixtures/example.yml.enc
diffcrypt encrypt -k $(cat test/fixtures/master.key) test/fixtures/example.yml
```


### Ruby

A direct API is exposed so `Diffcrypt::Encryptor` can be used in any ruby project.

**NOTE:** This API may change any time until v1.0

```ruby
encryptor = Diffcrypt::Encryptor.new('99e1f86b9e61f24c56ff4108dd415091')
yaml = File.read('test/fixtures/example.yml')
encrypted = encryptor.encrypt(yaml)
File.write('tmp/example.yml.enc', encrypted)
```

```ruby
encryptor = Diffcrypt::Encryptor.new('99e1f86b9e61f24c56ff4108dd415091')
yaml = File.read('test/fixtures/example.yml.enc')
config = YAML.safe_load(encryptor.decrypt(yaml))
```

### Ruby on Rails

Currently there is not native support for rails, but ActiveSupport can be monkeypatched to override
the built in encrypter. All existing `rails credentials:edit` also work with this method.

```ruby
require 'diffcrypt/rails/encrypted_configuration'
module Rails
  class Application
    def encrypted(path, key_path: 'config/master.key', env_key: 'RAILS_MASTER_KEY')
      Diffcrypt::Rails::EncryptedConfiguration.new(
        config_path: Rails.root.join(path),
        key_path: Rails.root.join(key_path),
        env_key: env_key,
        raise_if_missing_key: config.require_master_key,
      )
    end
  end
end
```



## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).



## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/marcqualie/diffcrypt.



## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).
