# emarsys-rb [![Build Status](https://travis-ci.org/Absolventa/emarsys-rb.svg?branch=master)](https://travis-ci.org/Absolventa/emarsys-rb)

Simple Ruby wrapper for the Emarsys API.

## Installation

    gem install emarsys

## Emarsys API Hint

This wrapper tries to implement all available methods of the Emarsys API in a
Ruby-like fashion. However, the Emarsys API lacks a decent amount of methods that
you expect an API to provide.
Thus, if methods are missing or a certain implementation
style was choosen it is most likely due to the inconsistency of the API itself.
Feel free to get in touch or submit a pull request if you encounter any problems.

Must-known facts about the Emarsys API:

* Emarsys uses internal IDs as field identifiers. E.g. 'email' is mapped to the value 3.
This gem tries to work around this by letting you specify a field mapping constant.
* certain methods require the specification of a key-field, e.g. the email (internally refered to as '3' again).
* Return values differ from method to method due to the way the Emarsys API is implemented.
Thus, a Hash as a return value or an Array of Hashes was choosen as the global return object. Basically it is a parsed JSON response.
* Please refer to the Emarsys API documentation for detailed information on parameters, return values or error codes.

## Configuration and Setup
### Authentication

Authenticate with the api credentials provided by your Emarsys account manager.

```ruby
Emarsys.configure do |c|
  c.api_username = 'my_username'
  c.api_password = 'my_password'
  # OPTIONAL, defaults to https://api.emarsys.net/api/v2
  c.api_endpoint = 'https://suite5.emarsys.net/api/v2'
end
```

### Field Mapping

As said before, Emarsys loves IDs. For using an API, they are evil. This gem provides
an easy way to adjust the individual field settings. Internally a Ruby Mapping Constant is used,
which that can be overwritten. It will be picked up automatically. E.g.:

```ruby
# Complete overwrite
Emarsys::FieldMapping::ATTRIBUTES = [
  {:id => 0, :identifier => 'interests', :name => 'Interests'},
  {:id => 1, :identifier => 'firstname', :name => 'First Name'},
  {:id => 2, :identifier => 'lastname',  :name => 'Last Name'},
]

# Add to the Mapping-Constant
Emarsys::FieldMapping::ATTRIBUTES << {:id => 100, :identifier => 'user_id', :name => "User-ID"}
```

All Emarsys predefined system fields are prefixed with an underscore, e.g. '_firstname' or '_revenue' in order to not
clash with individual mappings.


## Interacting with the API

You can interact with the API on the provided data objects:

#### Condition

```ruby
# Get all conditions
Emarsys::Condition.collection
```

#### Contact

```ruby
# Create a contact with custom key_field (one example with mapped identifier, one without)
Emarsys::Contact.create('user_id', 10, {firstname: "Jane", lastname: "Doe", email: "jane.doe@example.com"})
Emarsys::Contact.create(4980, 10, {1 => "Jane", 2 => "Doe", 3 => "jane.doe@example.com"})

# Update a contact with key_field (one example with mapped identifier, one without)
Emarsys::Contact.update('email', "jane.doe@example.com", {firstname: "John", lastname: "Doe"})
Emarsys::Contact.update(3, "jane.doe@example.com", {1 => "John", 2 => "Doe"})
```

#### ContactList

```ruby
# Get all contact_lists
Emarsys::ContactList.collection

# Create a contact list
Emarsys::ContactList.create
```

#### Emails

```ruby
# Get all email campaigns, optional filter
Emarsys::Email.collection
Emarsys::Email.collection(status: 3)

# Get a single email resource
Emarsys::Email.resource(1)

# Create a new email campaign
Emarsys::Email.create({})

Emarsys::Email.launch({})
```

#### Event

```ruby
Emarsys::Event.collection

# Trigger a custom event
Emarsys::Event.trigger(65, 3, ["test@example.com"])

# Trigger a custom event which actually sends a mail
# (Emarsys way to send transactional mails with placeholders)
Emarsys::Event.trigger(2, 3, 'test@example.com', {:global => {:my_placeholder => "some content"}})
```

#### Export

```ruby
Emarsys::Export.resource(1)
```

#### Field

```ruby
Emarsys::Field.collection
Emarsys::Field.choice(1)
```

#### Folder

```ruby
# Get all forms, optional filter parameters
Emarsys::Folder.collection
Emarsys::Folder.collection(:folder => 3)
```

#### Form

```ruby
# Get all forms
Emarsys::Form.collection
```

#### Language

```ruby
# Get all languages
Emarsys::Language.collection
```

#### Segment

```ruby
# Get all segments
Emarsys::Segment.collection
```

#### Source

```ruby
# Get all sources
Emarsys::Source.collection

# Create a new source
Emarsys::Source.create("New Source")

# Destroy a source
Emarsys::Source.destroy(123)
```

Please refer to the code for detailed instructions of each method.

## Ruby Versions

This gem was developed and tested with versions 1.9.3 and 2.0.0

## Changelog

### HEAD (not yet released)

* Update default endpoint fom `suite5.emarsys.net/api/v2` to `api.emarsys.net/api/v2`

### v0.1.0

Initial version

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request


## Copyright

Copyright (c) 2013 Daniel Schoppmann. See LICENSE.txt for details.
