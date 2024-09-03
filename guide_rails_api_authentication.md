# Guide: Rails API Authentication

## Part 1: Signup (create a new person):

1. In the `Gemfile`, uncomment the `bcrypt` gem (which helps hash passwords):

   ```ruby
   # Use ActiveModel has_secure_password
   gem 'bcrypt', '~> 3.1.7'
   ```

2. In the terminal, run bundle install to install the gem and restart your rails server

   ```
   bundle install
   rails server
   ```

   (Note: If you have issues here, you may need to install xcode command line tools by running the command `xcode-select --install` first, then enter the above commands again..)

3. In `app/models/person.rb`, add the method `has_secure_password` and validations:

   ```ruby
   has_secure_password
   validates :email, presence: true, uniqueness: true
   ```

4. In the terminal, create a people controller:

   ```
   rails generate controller people
   ```

5. In `test/controllers/people_controller_test.rb`, add the test
   ```ruby
   test "create" do
     assert_difference "person.count", 1 do
       post "/people.json", params: { name: "Test", email: "test@test.com", password: "password", password_confirmation: "password" }
       assert_response 201
     end
   end
   ```

6. In `config/routes.rb`, add the person create route:

   ```ruby
     post "/people" => "people#create"
   ```

7. In `app/controllers/people_controller.rb`, add a create action:

   ```ruby
     def create
       person = person.new(
         name: params[:name],
         email: params[:email],
         password: params[:password],
       )
       if person.save
         render json: { message: "Person created successfully" }, status: :created
       else
         render json: { errors: person.errors.full_messages }, status: :bad_request
       end
     end
   ```

8. In `app/controllers/application_controller.rb`, check to see if you have the `protect_from_forgery` line inside the ApplicationController class (it may already be there if you built a create action in the app). If it's not there, paste the line inside the ApplicationController class.

    ```ruby
      protect_from_forgery with: :exception, unless: -> { request.format.json? }
    ```
    
9. In the terminal, run `rails test` to make sure the code is working
    > You can also use a tool like curl or HTTPie to make a POST request to localhost:3000/people.json with the appropriate parameters and see the response
    > 
    > You CANNOT test this in the browser address bar (since the browser defaults to a GET request)
    > 
    > In your terminal, use git to save a version of your code.
    > ```bash
    > git add --all
    > git commit -m "Add people create action (signup)"
    > ```

## Part 2: Login (create JSON web tokens):

1. In the `Gemfile`, add the `jwt` gem (which helps create JSON web tokens):

   ```ruby
   gem 'jwt'
   ```

2. In the terminal, run `bundle install` to install the gem and restart your rails server

   ```
   bundle install
   rails server
   ```

3. In the terminal, create a sessions controller:

   ```
   rails generate controller sessions
   ```

4. In `test/controllers/sessions_controller_test.rb`, add the test
   ```ruby
   test "create" do
     post "/people.json", params: { name: "Test", email: "test@test.com", password: "password", password_confirmation: "password" }
     post "/sessions.json", params: { email: "test@test.com", password: "password" }
     assert_response 201

     data = JSON.parse(response.body)
     assert_equal ["jwt", "email", "person_id"], data.keys
   end
   ```

5. In `config/routes.rb`, add the sessions create route:

   ```ruby
     post "/sessions" => "sessions#create"
   ```

6. In `app/controllers/sessions_controller.rb`, add a create action:

   ```ruby
   def create
     person = person.find_by(email: params[:email])
     if person && person.authenticate(params[:password])
       jwt = JWT.encode(
         {
           person_id: person.id, # the data to encode
           exp: 24.hours.from_now.to_i # the expiration time
         },
         Rails.application.credentials.fetch(:secret_key_base), # the secret key
         "HS256" # the encryption algorithm
       )
       render json: { jwt: jwt, email: person.email, person_id: person.id }, status: :created
     else
       render json: {}, status: :unauthorized
     end
   end
   ```

   Notes:

   - This create action is responsible for finding the person in the database with the matching email and password, then creating a JSON Web Token using the `JWT.encode` method from the `jwt` gem.

     - You could clean up the controller here by moving the logic of creating a JWT into a separate class (for examples, read more [here](https://www.thegreatcodeadventure.com/jwt-auth-in-rails-from-scratch/) or [here](https://www.pluralsight.com/guides/token-based-authentication-with-ruby-on-rails-5-api)).

   - If you’re using an older version of Rails (before 5.2.0), change the method `credentials` to `secrets` within the `JWT.encode` method:
     `Rails.application.secrets.fetch(:secret_key_base)`

9. In the terminal, run `rails test` to make sure the code is working
   > You can also use a tool like curl or HTTPie to make a POST request to localhost:3000/sessions.json with the appropriate parameters and see the response
   > 
   > You CANNOT test this in the browser address bar (since the browser defaults to a GET request)
   > 
   > In your terminal, use git to save a version of your code.
   > ```bash
   > git add --all
   > git commit -m "Add sessions create action (login)"
   > ```

## Part 3: Backend person authentication

1. In `app/controllers/application_controller.rb`, add the `current_person` and `authenticate_person` helper methods:

   ```ruby
     def current_person
       auth_headers = request.headers["Authorization"]
       if auth_headers.present? && auth_headers[/(?<=\A(Bearer ))\S+\z/]
         token = auth_headers[/(?<=\A(Bearer ))\S+\z/]
         begin
           decoded_token = JWT.decode(
             token,
             Rails.application.credentials.fetch(:secret_key_base),
             true,
             { algorithm: "HS256" }
           )
           person.find_by(id: decoded_token[0]["person_id"])
         rescue JWT::ExpiredSignature
           nil
         end
       end
     end

     def authenticate_person
       unless current_person
         render json: {}, status: :unauthorized
       end
     end
   ```

   This gives all your Rails controllers access to the methods `current_person` and `authenticate_person`.

Now you can include a JWT in the headers of any web request you want to be logged into (the header key must be Authorization and the value must start with Bearer).

Example HTTP request (with a JWT in the request headers)

```
### Photos create
POST http://localhost:3000/photos.json
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjozLCJleHAiOjE2Mjk5OTI4NjR9.G2ExsrDYn3oE0vJkvm4T6Po2GbNpH5cqTEaVPVuK_MA
Content-Type: application/json

{
  "name": "Test name",
  "width": 640,
  "height": 480
}
```

Any web request that includes a valid JWT in the headers in this fashion has access to the `current_person` variable in both the controller and the view.

To logout, simply remove the JWT from any Authorization request headers.
