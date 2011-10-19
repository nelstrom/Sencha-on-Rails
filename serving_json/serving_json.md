!SLIDE

# SERVING JSON

!SLIDE commandline incremental

## JAVASCRIPT

    @@@javascript
    $ parseInt("042", 10)
    > 42
    $ 42.toString()
    > "42"

!SLIDE commandline incremental

## RUBY

    @@@ruby
    $ "042".to_i
    => 42
    $ 42.to_s
    => "42"

!SLIDE commandline incremental

## Serialization

    @@@ruby
    $ car = {:make => "bmw", :year => "2003"}
    => {:make=>"bmw", :year=>"2003"}
    $ car.to_json
    NoMethodError: undefined method `to_json'
    $ require 'json'
    => true
    $ car.to_json
    => "{"make":"bmw","year":"2003"}"

!SLIDE

## wow!
