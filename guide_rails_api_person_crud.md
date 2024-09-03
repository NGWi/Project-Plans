# Guide: Rails API CRUD practice

## Create a new Rails app
1.  Start with a clean slate

    - Close all your text editor windows, quit your text editor.
    - Find and kill any terminal Rails servers using Ctrl C.
    - Open Terminal and navigate to your Actualize folder.

2.  In your terminal, create a new rails project:

    <pre><code>rails new <ins><strong>name-of-your-app</strong></ins></code></pre>

3.  In the terminal, go to your new directory

    <pre><code>cd <ins><strong>name-of-your-app</strong></ins></code></pre>

4.  In the terminal, create the database by entering:

    ```
    rails db:create
    ```

5. In the terminal, start your server by entering:

    ```
    rails server
    ```

6. In your browser, visit http://localhost:3000 to make sure your app is working

> In your terminal, open a new tab by entering the shortcut Command T, then use git to save a version of your code.
> ```bash
> git init
> git add --all
> git commit -m "Initial commit"
> ```

## Create the data layer
1.  In the terminal, create a new model by entering:

  <pre><code>rails generate model <ins><strong>person</strong></ins> <ins><strong>first_name</strong></ins>:string <ins><strong>last_name</strong></ins>:string <ins><strong>email</strong></ins>:string</code></pre> 
  <ins><strong>phone_number</strong></ins>:string <ins><strong>short_bio</strong></ins>:text <ins><strong>linkedin_url</strong></ins>:string <ins><strong>twitter_handle</strong></ins>:string <ins><strong>personal_blog_url</strong></ins>:string <ins><strong>online_resume_url</strong></ins>:string <ins><strong>github_url</strong></ins>:string <ins><strong>photo</strong></ins>:string <ins><strong>password_digest

2.  In the terminal, enter the command to open your text editor:

    ```
    code .
    ```

3.  In your text editor, double check the generated migration code in the `db/migrate` folder

    - If you have any typos, you can fix them now

4.  In the terminal, run the migration code by entering:

    ```
    rails db:migrate
    ```

5. In your text editor, use the `rails console` or the `db/seeds.rb` file to create new items:

    <pre><code>
    <ins><strong>person</strong></ins>.create(<ins><strong>first_name: "Jane", last_name: "Doe", email: "janedoe@example.com", password:"password"</strong></ins>)
    <ins><strong>person</strong></ins>.create(<ins><strong>first_name: "John", last_name: "Doe", email: "johndoe@example.com", password:"password"</strong></ins>)</code></pre>

6. In the terminal, run the code in the seeds file by entering:

    ```
    rails db:seed
    ```

7. In the terminal, open the irb rails console by entering:

    ```
    rails console
    ```

8. In the terminal (irb rails console), you can double check your entries by entering:

    <pre><code><ins><strong>person</strong></ins>.all</code></pre>

9. In your terminal (irb rails console), exit the rails console by entering:

    ```
    exit
    ```
    
> In your terminal, use git to save a version of your code.
> ```bash
> git add --all
> git commit -m "Add model with seeds for initial data"
> ```

## Create the web layer

1. In the terminal, create JSON views by entering:

    <pre><code>rails generate jbuilder <ins><strong>person</strong></ins></code></pre>

2. Define the JSON output 
    - <details><summary>In <code>app/views/<ins><strong>people</strong></ins>/<ins><strong>_person</strong></ins>.json.jbuilder</code>, replace the code with your model's attributes</summary>
  
      ```ruby
        json.id person.id
        json.first_name person.first_name
        json.last_name person.last_name
        json.email person.email
        json.phone_number person.phone_number
        json.short_bio person.short_bio
        json.linkedin_url person.linkedin_url
        json.twitter_handle person.twitter_handle
        json.personal_blog_url person.personal_blog_url
        json.online_resume_url person.online_resume_url
        json.github_url person.github_url
        json.photo person.photo
        json.password_digest person.password_digest
        json.created_at person.created_at
        json.updated_at person.updated_at
      ```
      </details>
      
3.  In the terminal, create a new controller by entering:

    <pre><code>rails generate controller <ins><strong>people</strong></ins></code></pre>

4. Make the **INDEX** action
    - In <code>test/controllers/<ins><strong>people</strong></ins>_controller_test.rb</code>, add a test for the index action
  
      <pre><code>  test "index" do
          get "/<ins><strong>people</strong></ins>.json"
          assert_response 200

          data = JSON.parse(response.body)
          assert_equal <ins><strong>person</strong></ins>.count, data.length
        end</code></pre>
      
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        get "/people" => "people#index"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>people</strong></ins>_controller.rb</code>, add an index action</summary>
  
      ```ruby
        def index
          @people = person.all
          render :index
        end
      ```
  
      </details>
    - In the terminal, run `rails test` to make sure the code is working
      > You can also use a tool like [curl](https://curl.se/docs/manpage.html), [Postman](https://www.postman.com/), or [HTTPie](https://httpie.io/product/) to make a GET request to <code>localhost:3000/<ins><strong>people</strong></ins>.json</code> and see the response
      > 
      > You can also visit <code>localhost:3000/<ins><strong>people</strong></ins>.json</code> in the browser to see the output
      > 
      > In your terminal, use git to save a version of your code.
      > ```bash
      > git add --all
      > git commit -m "Add index action"
      > ```

5. Make the **CREATE** action
    - In <code>test/controllers/<ins><strong>people</strong></ins>_controller_test.rb</code>, add a test for the create action
  
      <pre><code>  test "create" do
          assert_difference "<ins><strong>Person</strong></ins>.count", 1 do
            post "/<ins><strong>people</strong></ins>.json", params: { <ins><strong>first_name: "Test", email: "test@test.com", password: "password", password_confirmation: "password"</strong></ins> }
            assert_response 200
          end
        end</code></pre>
      
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        post "/people" => "people#create"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>people</strong></ins>_controller.rb</code>, add a create action</summary>
  
      ```ruby
        def create
          @person = person.create(
            name: params[:name],
            width: params[:width],
            height: params[:height],
          )
          render :show
        end
      ```
  
      </details>
    - In `app/controllers/application_controller.rb`, add the following line to disable [Rails CSRF protection](https://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf) for JSON requests:
      ```diff
        class ApplicationController < ActionController::Base
      +   protect_from_forgery with: :exception, unless: -> { request.format.json? }
        end
      ```
    - In the terminal, run `rails test` to make sure the code is working
      > You can also use a tool like [curl](https://curl.se/docs/manpage.html), [Postman](https://www.postman.com/), or [HTTPie](https://httpie.io/product/) to make a POST request to <code>localhost:3000/<ins><strong>people</strong></ins>.json</code> with the appropriate parameters and see the response
      > 
      > You CANNOT test this in the browser address bar (since the browser defaults to a GET request)
      > 
      > In your terminal, use git to save a version of your code.
      > ```bash
      > git add --all
      > git commit -m "Add create action"
      > ```

6. Make the **SHOW** action
    - In <code>test/controllers/<ins><strong>people</strong></ins>_controller_test.rb</code>, add a test for the show action
  
      <pre><code>  test "show" do
          get "/<ins><strong>people</strong></ins>/#{<ins><strong>person</strong></ins>.first.id}.json"
          assert_response 200

          data = JSON.parse(response.body)
          assert_equal ["id", <ins><strong>"first_name", "last_name", "email",</strong></ins> "created_at", "updated_at"], data.keys
        end</code></pre>
      
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        get "/people/:id" => "people#show"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>people</strong></ins>_controller.rb</code>, add a show action</summary>
  
      ```ruby
        def show
          @person = person.find_by(id: params[:id])
          render :show
        end
      ```
  
      </details>
    - In the terminal, run `rails test` to make sure the code is working
      > You can also use a tool like [curl](https://curl.se/docs/manpage.html), [Postman](https://www.postman.com/), or [HTTPie](https://httpie.io/product/) to make a GET request to <code>localhost:3000/<ins><strong>people</strong></ins>/<ins><strong>1</strong></ins>.json</code> and see the response
      > 
      > You can also visit <code>localhost:3000/<ins><strong>people</strong></ins>/<ins><strong>1</strong></ins>.json</code> in the browser to see the output
      > 
      > In your terminal, use git to save a version of your code.
      > ```bash
      > git add --all
      > git commit -m "Add show action"
      > ```

7. Make the **UPDATE** action
    - In <code>test/controllers/<ins><strong>people</strong></ins>_controller_test.rb</code>, add a test for the update action
  
      <pre><code>  test "update" do
          <ins><strong>person</strong></ins> = <ins><strong>person</strong></ins>.first
          patch "/<ins><strong>people</strong></ins>/#{<ins><strong>person</strong></ins>.id}.json", params: { <ins><strong>first_name: "Updated name"</strong></ins> }
          assert_response 200

          data = JSON.parse(response.body)
          assert_equal <ins><strong>"Updated name", data["first_name"]</strong></ins>
        end</code></pre>
      
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        patch "/people/:id" => "people#update"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>people</strong></ins>_controller.rb</code>, add an update action</summary>
  
      ```ruby
        def update
          @person = person.find_by(id: params[:id])
          @person.update(
            name: params[:name] || @person.name,
            width: params[:width] || @person.width,
            height: params[:height] || @person.height,
          )
          render :show
        end
      ```
  
      </details>
    - In the terminal, run `rails test` to make sure the code is working
      > You can also use a tool like [curl](https://curl.se/docs/manpage.html), [Postman](https://www.postman.com/), or [HTTPie](https://httpie.io/product/) to make a PATCH request to <code>localhost:3000/<ins><strong>people</strong></ins>/<ins><strong>1</strong></ins>.json</code> with the appropriate parameters and see the response
      > 
      > You CANNOT test this in the browser address bar (since the browser defaults to a GET request)
      > 
      > In your terminal, use git to save a version of your code.
      > ```bash
      > git add --all
      > git commit -m "Add update action"
      > ```

8. Make the **DESTROY** action
    - In <code>test/controllers/<ins><strong>people</strong></ins>_controller_test.rb</code>, add a test for the update action
  
      <pre><code>  test "destroy" do
          assert_difference "<ins><strong>person</strong></ins>.count", -1 do
            delete "/<ins><strong>people</strong></ins>/#{<ins><strong>person</strong></ins>.first.id}.json"
            assert_response 200
          end
        end</code></pre>
      
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        delete "/people/:id" => "people#destroy"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>people</strong></ins>_controller.rb</code>, add a show action</summary>
  
      ```ruby
        def destroy
          @person = person.find_by(id: params[:id])
          @person.destroy
          render json: { message: "person destroyed successfully" }
        end
      ```
  
      </details>
    - In the terminal, run `rails test` to make sure the code is working
      > You can also use a tool like [curl](https://curl.se/docs/manpage.html), [Postman](https://www.postman.com/), or [HTTPie](https://httpie.io/product/) to make a DELETE request to <code>localhost:3000/<ins><strong>people</strong></ins>/<ins><strong>1</strong></ins>.json</code> with the appropriate parameters and see the response
      > 
      > You CANNOT test this in the browser address bar (since the browser defaults to a GET request)
      > 
      > In your terminal, use git to save a version of your code.
      > ```bash
      > git add --all
      > git commit -m "Add destroy action"
      > ```
