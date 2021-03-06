# OAuth2

[![Gem Version](https://badge.fury.io/rb/oauth2.png)][gem]
[![Build Status](https://secure.travis-ci.org/intridea/oauth2.png?branch=master)][travis]
[![Dependency Status](https://gemnasium.com/intridea/oauth2.png?travis)][gemnasium]
[![Code Climate](https://codeclimate.com/github/intridea/oauth2.png)][codeclimate]
[![Coverage Status](https://coveralls.io/repos/intridea/oauth2/badge.png?branch=master)][coveralls]

[gem]: https://rubygems.org/gems/oauth2
[travis]: http://travis-ci.org/intridea/oauth2
[gemnasium]: https://gemnasium.com/intridea/oauth2
[codeclimate]: https://codeclimate.com/github/intridea/oauth2
[coveralls]: https://coveralls.io/r/intridea/oauth2

A Ruby wrapper for the OAuth 2.0 specification. This is a work in progress,
being built first to solve the pragmatic process of connecting to existing
OAuth 2.0 endpoints (a.k.a. Facebook) with the goal of building it up to meet
the entire specification over time.

## Installation
    gem install oauth2

To ensure the code you're installing hasn't been tampered with, it's
recommended that you verify the signature. To do this, you need to add my
public key as a trusted certificate (you only need to do this once):

    gem cert --add <(curl -Ls https://gist.github.com/sferik/4701180/raw/public_cert.pem)

Then, install the gem with the high security trust policy:

    gem install oauth2 -P HighSecurity

## Resources
* [View Source on GitHub][code]
* [Report Issues on GitHub][issues]
* [Read More at the Wiki][wiki]

[code]: https://github.com/intridea/oauth2
[issues]: https://github.com/intridea/oauth2/issues
[wiki]: https://wiki.github.com/intridea/oauth2

## Usage Examples
    require 'oauth2'
    client = OAuth2::Client.new('client_id', 'client_secret', :site => 'https://example.org')

    client.auth_code.authorize_url(:redirect_uri => 'http://localhost:8080/oauth2/callback')
    # => "https://example.org/oauth/authorization?response_type=code&client_id=client_id&redirect_uri=http://localhost:8080/oauth2/callback"

    token = client.auth_code.get_token('authorization_code_value', :redirect_uri => 'http://localhost:8080/oauth2/callback', :headers => {'Authorization' => 'Basic some_password'})
    response = token.get('/api/resource', :params => { 'query_foo' => 'bar' })
    response.class.name
    # => OAuth2::Response

## OAuth2::Response
The AccessToken methods #get, #post, #put and #delete and the generic #request
will return an instance of the #OAuth2::Response class.

This instance contains a #parsed method that will parse the response body and
return a Hash if the Content-Type is application/x-www-form-urlencoded or if
the body is a JSON object.  It will return an Array if the body is a JSON
array.  Otherwise, it will return the original body string.

The original response body, headers, and status can be accessed via their
respective methods.

## OAuth2::AccessToken
If you have an existing Access Token for a user, you can initialize an instance
using various class methods including the standard new, from_hash (if you have
a hash of the values), or from_kvform (if you have an
application/x-www-form-urlencoded encoded string of the values).

## OAuth2::Error
On 400+ status code responses, an OAuth2::Error will be raised.  If it is a
standard OAuth2 error response, the body will be parsed and #code and #description will contain the values provided from the error and
error_description parameters.  The #response property of OAuth2::Error will
always contain the OAuth2::Response instance.

If you do not want an error to be raised, you may use :raise_errors => false
option on initialization of the client.  In this case the OAuth2::Response
instance will be returned as usual and on 400+ status code responses, the
Response instance will contain the OAuth2::Error instance.

## Authorization Grants
Currently the Authorization Code, Implicit, Resource Owner Password Credentials, Client Credentials, and Assertion
authentication grant types have helper strategy classes that simplify client
use.  They are available via the #auth_code, #implicit, #password, #client_credentials, and #assertion methods respectively.

    auth_url = client.auth_code.authorize_url(:redirect_uri => 'http://localhost:8080/oauth/callback')
    token = client.auth_code.get_token('code_value', :redirect_uri => 'http://localhost:8080/oauth/callback')

    auth_url = client.implicit.authorize_url(:redirect_uri => 'http://localhost:8080/oauth/callback')
    # get the token params in the callback and
    token = OAuth2::AccessToken.from_kvform(client, query_string)

    token = client.password.get_token('username', 'password')

    token = client.client_credentials.get_token

    token = client.assertion.get_token(assertion_params)

If you want to specify additional headers to be sent out with the
request, add a 'headers' hash under 'params':

    token = client.auth_code.get_token('code_value', :redirect_uri => 'http://localhost:8080/oauth/callback', :headers => {'Some' => 'Header'})

You can always use the #request method on the OAuth2::Client instance to make
requests for tokens for any Authentication grant type.

## Supported Ruby Versions
This library aims to support and is [tested against][travis] the following Ruby
implementations:

* Ruby 1.8.7
* Ruby 1.9.2
* Ruby 1.9.3
* Ruby 2.0.0
* [JRuby][]
* [Rubinius][]

[jruby]: http://jruby.org/
[rubinius]: http://rubini.us/

If something doesn't work on one of these interpreters, it's a bug.

This library may inadvertently work (or seem to work) on other Ruby
implementations, however support will only be provided for the versions listed
above.

If you would like this library to support another Ruby version, you may
volunteer to be a maintainer. Being a maintainer entails making sure all tests
run and pass on that implementation. When something breaks on your
implementation, you will be responsible for providing patches in a timely
fashion. If critical issues for a particular implementation exist at the time
of a major release, support for that Ruby version may be dropped.

## License
Copyright (c) 2011-2013 Michael Bleigh and Intridea, Inc. See [LICENSE][] for
details.

[license]: LICENSE.md
