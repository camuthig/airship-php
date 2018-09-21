<p align="center">
  <a href="https://airshiphq.com/" target="_blank">
    <img  alt="Airship" src="https://avatars3.githubusercontent.com/u/29476417?s=200&v=4" class="img-responsive">
  </a>
</p>



# Airship PHP

## Requirement
PHP 5.5 or higher

## Installation
`php composer.phar require airship/airship-php`


## Usage
```php
require 'vendor/autoload.php';

// Create an instance with an env key
$airship = new Airship\Airship(new Airship\Client\GuzzleClient('<env_key>'));


if ($airship->flag('bitcoin-pay')->isEnabled(['id' => 5])) {
  // ...
}

// Define your entity
$entity = [
  'type' => 'User', // 'type' starts with a capital letter '[U]ser', '[H]ome', '[C]ar'. If omittied, it will default to 'User'
  'id' => '1234', // 'id' must be a string or integer
  'display_name' => 'ironman@stark.com' // must be a string. If omitted, the SDK will use the same value as 'id' (converted to a string)
]
// or
$entity = new Target(1234, 'User', 'ironman@stark.com');

// The most compact form can be:
$entity = [
  'id' => 1234
]
// or
$entity = new Target(1234);

// as this will translate into:
$entity = [
  'type' => 'User',
  'id' => '1234',
  'display_name' => '1234'
]

$airship->flag('bitcoin-pay')->isEnabled($entity) // Does the entity have the feature 'bitcoin-pay'?
$airship->flag('bitcoin-pay')->getTreatment($entity) // Get the treatment associated with the flag
$airship->flag('bitcoin-pay')->isEligible($entity)
// Returns true if the entity can potentially receive the feature via sampling
// or is already receiving the feature.

// Note: It may take up to a minute for entities gated to show up on our web app.
```


## Attributes (for complex targeting)
```php
// Define your entity with an attributes dictionary of key-value pairs.
// Values must be a string, a number, or a boolean. nil values are not accepted.
// For date or datetime string value, use iso8601 format.
$entity = [
  'type' => 'User',
  'id' => '1234',
  'display_name' => 'ironman@stark.com',
  'attributes' => [
    't_shirt_size' => 'M',
    'date_created' => '2018-02-18',
    'time_converted' => '2018-02-20T21:54:00.630815+00:00',
    'owns_property' => true,
    'age' => 39
  ]
]
// or
$entity = new Target(
  1234,
  'User',
  'ironman@stark.com',
  [
    't_shirt_size' => 'M',
    'date_created' => '2018-02-18',
    'time_converted' => '2018-02-20T21:54:00.630815+00:00',
    'owns_property' => true,
    'age' => 39
  ]
);

// Now in app.airshiphq.com, you can target this particular user using its
// attributes
```

## Group (for membership-like cascading behavior)
```php
// An entity can be a member of a group.
// The structure of a group entity is just like that of the base entity.
$entity = [
  'type' => 'User',
  'id' => '1234',
  'display_name' => 'ironman@stark.com',
  'attributes' => [
    't_shirt_size' => 'M',
    'date_created' => '2018-02-18',
    'time_converted' => '2018-02-20T21:54:00.630815+00:00',
    'owns_property' => true,
    'age' => 39
  ],
  'group' => [
    'type' => 'Club',
    'id' => '5678',
    'display_name' => 'SF Homeowners Club',
    'attributes' => [
      'founded' => '2016-01-01',
      'active' => true
    ]
  ]
]
// or
$group = new Target(
  5678,
  'Club',
  'SF Homeowners Club',
  [
    'founded' => '2016-01-01',
    'active' => true,
  ]
);
$user = new Target(
  1234,
  'User',
  'ironman@stark.com',
  [
    't_shirt_size' => 'M',
    'date_created' => '2018-02-18',
    'time_converted' => '2018-02-20T21:54:00.630815+00:00',
    'owns_property' => true,
    'age' => 39
  ],
  $group
);

// Inheritance of values `isEnabled`, `getTreatment`, `getPayload`, and `isEligible` works as follows:
// 1. If the group is enabled, but the base entity is not,
//    then the base entity will inherit the values `isEnabled`, `getTreatment`, `getPayload`, and `isEligible` of the group entity.
// 2. If the base entity is explicitly blacklisted, then it will not inherit.
// 3. If the base entity is not given a variation in rule-based variation assignment,
//    but the group is and both are enabled, then the base entity will inherit
//    the variation of the group's.


// You can ask questions about the group directly (use the `is_group` flag):
$entity = [
  'is_group' => true,
  'type' => 'Club',
  'id' => '5678',
  'display_name' => 'SF Homeowners Club',
  'attributes' => [
    'founded' => '2016-01-01',
    'active' => true
  ]
]

$airship->flag('bitcoin-pay')->isEnabled($entity)
```
___

# License
 [MIT](/LICENSE)

[![StackShare](https://img.shields.io/badge/tech-stack-0690fa.svg?style=flat)](https://stackshare.io/airship/airship)
