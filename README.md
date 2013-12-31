# RSpec::Rails::ModelMocks [![Build Status](https://secure.travis-ci.org/thomas-holmes/rspec-rails-model_mocks.png?branch=master)](http://travis-ci.org/thomas-holmes/rspec-rails-model_mocks)

RSpec::Rails::ModelMocks provides tools for testing `ActiveRecord` models.

    mock_model(Person, name: "Fred")

## Install

Add this line to your application's gemfile:

    gem 'rspec-rails-model_mocks', git: 'https://github.com/thomas-holmes/rspec-rails-model_mocks'

And then execute:

    $ bundle

If you are already using `rspec-rails` and have run the installation
generators then `mock_model` and `stub_model` will be available. Otherwise
you will need to follow [`rspec-rails`](https://github.com/rspec/rspec-rails) installation instructions
and run:

    rails generate rspec:install

## Usage

### Mock

Creates a test double representing `string_or_model_class` with common
ActiveModel methods stubbed out. Additional methods may be easily stubbed
(via `add_stubs`) if `stubs` is passed. This is most useful for impersonating
models that don't exist yet.

NOTE that only ActiveModel's methods, plus `new_record?`, are stubbed out
implicitly. `new_record?` returns the inverse of `persisted?`, and is
present only for compatibility with extension frameworks that have yet to
update themselves to the ActiveModel API (which declares `persisted?`,
not `new_record?`).

`string_or_model_class` can be any of:

  * A String representing a Class that does not exist
  * A String representing a Class that extends `ActiveModel::Naming`
  * A Class that extends `ActiveModel::Naming`

### Stub

Creates an instance of `Model` with `to_param` stubbed using a
generated value that is unique to each object. If `Model` is an
`ActiveRecord` model, it is prohibited from accessing the database.

For each key in `stubs`, if the model has a matching attribute
(determined by `respond_to?`) it is simply assigned the submitted values.
If the model does not have a matching attribute, the key/value pair is
assigned as a stub return value using RSpec's mocking/stubbing
framework.

`persisted?` is overridden to return the result of `!id.nil?`
This means that by default `persisted?` will return true. If you want
the object to behave as a new record, sending it `as_new_record` will
set the id to nil. You can also explicitly set `:id => nil`, in which
case `persisted?` will return false, but using `as_new_record` makes the
example a bit more descriptive.

While you can use `stub_model` in any example (model, view, controller,
helper), it is especially useful in view examples, which are
inherently more state-based than interaction-based.

    stub_model(Person)
    stub_model(Person).as_new_record
    stub_model(Person, :to_param => 37)
    stub_model(Person) {|person| person.first_name = "David"}
