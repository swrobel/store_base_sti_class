[![Build Status](https://travis-ci.org/appfolio/store_base_sti_class.svg?branch=master)](https://travis-ci.org/appfolio/store_base_sti_class)
## Description

Given the following class definitions:

```ruby
class Address
  belongs_to :addressable, :polymorphic => true
end

class Person
  has_many :addresses, :as => addressable
end

class Vendor < Person
end
```

and given the following code:

```ruby
vendor = Vendor.create(...)
address = vendor.addresses.create(...)

p vendor
p address
```

will output:

```ruby
#<Vendor id: 1, type: "Vendor" ...>
#<Address id: 1, addressable_id: 1, addressable_type: 'Person' ...>
```

Notice that addressable_type column is Person even though the actual class is Vendor.

Normally, this isn't a problem, however, it can have negative performance
characteristics in certain circumstances. The most obvious one is that a join
with persons or an extra query is required to find out the actual type of
addressable.

This gem adds the ActiveRecord::Base.store_base_sti_class configuration
option. It defaults to true for backwards compatibility. Setting it to false
will alter ActiveRecord's behavior to store the actual class in polymorphic
_type columns when STI is used.

In the example above, if the ActiveRecord::Base.store_base_sti_class is false, the output will be,

```
  #<Vendor id: 1, type: "Vendor" ...>
  #<Address id: 1, addressable_id: 1, addressable_type: 'Vendor' ...>
```

## Usage

Add the following line to your Gemfile,

```ruby
gem 'store_base_sti_class'
```

then bundle install. Once you have the gem installed, add the following to one
of the initializers (or make a new one) in config/initializers,

    ActiveRecord::Base.store_base_sti_class = false

When changing this behavior, you will have write a migration to update all of
your existing _type columns accordingly. You may also need to change your
application if it explicitly relies on the _type columns.

## Notes

This gem incorporates work from:

- https://github.com/codepodu/store_base_sti_class_for_4_0

It currently works with ActiveRecord 4.2.x through 6.0.x. If you need support
for ActiveRecord 3.x, use a pre-1.0 version of the gem, or ActiveRecord < 4.2
use a pre-2.0 version of the gem.

## Copyright

Copyright (c) 2011-2019 AppFolio, inc. See LICENSE.txt for
further details.
