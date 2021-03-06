# Buffer

Official API wrapper for [Buffer](http://bufferapp.com) covering user & profile management and adding, removing & editing updates, and more planned endpoints. See the [full API Documentation](http://bufferapp.com/developers/api/) for more.

For a Buffer OmniAuth strategy for authenticating your users with Buffer, see [omniauth-buffer2](/bufferapp/omniauth-buffer2).

## Installation

Add this line to your application's Gemfile:

    gem 'buffer'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install buffer

## Usage

### Client

The most basic usage of the wrapper involves creating a `Buffer::Client` and then making requests through that object. Since all requests to the Buffer API require an access token you must have first authorized a Buffer user, or otherwise have an access token. The [Buffer OmniAuth Strategy](http://github.com/bufferapp/omniauth-buffer2) can help with this.

Use the Client if you just want to have full control over your `get` and `post` requests.

Creating a new client is simple:

```ruby
buffer = Buffer::Client.new access_token
```

### User

The User object makes working with users easier by providing some useful shortcuts to user information, like `id`, and data, like `profiles`. It provides the all the methods specified in the Client as it inherits from it.

The User introduces some caching of requests. These are invalidated when a `post` request is made to an endpoint that might affect the data. You can force cache invalidation of one or all endpoints using the `invalidate` method.

**Note: Currently only `invalidate` is implemented. If you make a POST request that changes a user object you must manually invalidate the cache**.

Creating a new user:

```ruby
user = Buffer::User.new access_token
```

## API

You can use a client, or any subclass, to make GET & POST requests to the Buffer API. The exposed API methods are `get`, `post` and, the lowest level, `api`. 

#### api

`api` is the method at the root of the API, handling all requests.

```ruby
api (symbol) method, (string) url, (hash, optional) params, (hash or array, optional) data
# for example:
buffer.api :get, 'user'
```

`method` must be either `:get` or `:post`

#### get

```ruby
user_data = buffer.get 'user'
user_profiles = buffer.get 'profiles'
```

`get` is just a thin layer over `api`, so the above is equivalent to:

```ruby
user_data = buffer.api :get, 'user'
user_profiles = buffer.api :get, 'profiles'
```

#### `post`

```ruby
user_data = buffer.post 'updates/create', :text => "Hello, world!", :profile_ids => ['123abc456', '789def123']
```

`post` is also a wrapper for `api`, so the above becomes:

```ruby
user_data = buffer.api :post, 'updates/create', :text => "Hello, world!", :profile_ids => ['123abc456', '789def123']
```

## User API

#### `id`, `created_at`...

The User object allows access to the data from the user endpoint in manner of a normal ruby accessor. This makes accessing user info very easy, like the following:

```ruby
user.id
user.created_at
```

#### `profiles` **not implemented**

`profiles` is a helper method that gives you access to the profiles associated with a particular user. It's shorthand for `get 'profiles'` with caching. The

`profiles` is invalidated by any `post` request made to a child of the `profiles` endpoing, like `/profiles/:id/schedules/update`.

```ruby
user.profiles                 # all a user's profiles
user.profiles '123abc456def'  # return a profile with a specific id
```

## Todo

* `user.profiles`
* Automatic cache invalidation after post request
* Move cache handling to a mixin