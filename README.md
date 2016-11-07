# railsStarterGuide

Rails App Checklist

1. Create it: rails new appName -d postgresql
-

	***Check: Run “rails server” to see if the welcome to rails 	message shows at localhost:3000

2. Make your database: created dbName_development
--
	a. The name_development is a part of rails conventions

	***Check: get into postgres with “psql,” the connect to your database with “\c db_name”
	
3. Create controller: rails generate controller controllerNames
--
	a. make sure the controller name is plural to follow rails conventions

	***Check: navigate to app >> controllers to see if your controller is there

4. Create Model: rails generate model modelName field1Name:dataType field2Name:dataType…etc.
--
		a. Model names are singular
		b. If creating a foreign key, do “modelName:references”

	***Check: Navigate to db >> migrate to see if there is a file, click on it to see if it contains your schema

5. Migrate your database: rake db:migrate
--

	***Check: Navigate to db >> schema.rb to see if your 	schema was loaded in

— Repeat steps 3-5 as many times as necessary — 

6. Create seed data: An example below:
--
	
	House.destroy_all
	Character.destroy_all

	house1 = House.create(name: "Targaryen")
	house2 = House.create(name: "Zehnder")

	character1 = Character.create(name: "Rob", indulgence: "whores", house: house1)

	character2 = Character.create(name: "Jeff", indulgence: "whores", house: house2)

	character3 = Character.create(name: "Omar", indulgence: "violence", house: house1)

	character4 = Character.create(name: "Jimmy", indulgence: "alcohol", house: house2)
	

	*** Make sure that you do ClassName.create not Class name.new in the seed data or else it won’t work.

7. Seed your database: rake db:seed
--
	
	***Check: open postgres, then open your database. do a “Select * from table_name;” to see if your data made it over. 

8. Modify Your Routes to Reflect Your Controllers: Add the following for your routes file…
--

	resources :controllerName (make sure it ends with an “s” for convention purposes. Repeat as necessary for each of your models.

	***Check: add “root “modelname#index” to have the home page default to this index view for this controller >> Helps to set a location to create a test page in the next step

9. Create Home Page View: Make a hello world view for the index page by modifying the index.html.erb view under your first model’s views folder
--

10. View your routes: Run the “rake routes” command in terminal
--

	— New Page Process —

	1. Create view under the views >> controllerName folder using “viewName.html.erb” as the name of the document

	2. Create the action inside the controller file >> 
		def whatever
			“stuff to send into the view”
		end

	3. Modify the view, so that it reflects whatever you want it to return in the HTML for that particular route

	— End New Page Process —


11. Create a home page for your index view using the new page process: Modify the page to however you like to display all the options
--
    1. Need to create an index action in your controller
    2. Modify the view

	<% @houses.each do |house| %>
	<p><%= link_to house.name, house_path(house.id) %></p>
	<% end %>

	>> The output here is links to the show pages of each house with their names listed as the clickable link. Link to works like this:

	link_to “Text you want displayed in the anchor tag”, “the path you want the link to navigate to”

	because I used rake routes, I know that house_path leads to the show page,

		Route : house GET    /houses/:id(.:format)          houses#show

	so I have to structure my route, so that I go to the house path and pass in the house id as a param, hence, “house_path(house.id)”

	This should lead you to an error that asks for your show page view

12. Create the Show Page
--

	— New Page Process —

	Identify desired path: 

	house GET    /houses/:id(.:format)          houses#show

	1. Create view under the views >> controllerName folder using “show.html.erb” as the name of the document

	2. Create the action inside the controller file >> 
		def show
			code goes here
		end

	3. Modify the view, so that it reflects whatever you want it to return in the HTML for that particular route

		<h1>The goons of <%=@house.name%></h1>
			<% @house.characters.each do |person| %>
			<p>Person: <%= person.name %> Indulgence: <%= person.indulgence %> </p>
			<% end %>

	— End New Page Process —

13. Create the edit page
--

	— New Page Process —

	Identify desired path:

	edit_house GET    /houses/:id/edit(.:format)     houses#edit

	1. Create view under the views >> controllerName folder using “edit.html.erb” as the name of the document

	2. Create the action inside the controller file >> 
		def edit
			code goes here
		end

	3. Modify the view, so that it reflects whatever you want it to return in the HTML for that particular route

	— End New Page Process —

	*** Special step for any view requiring a form. If you know that you can use this form more than once, you can create a form reference page.

	Add form content (example below) 

		<%= form_for @house do |f| %>

			<p>
				<%= f.label :name %><br>
				<%= f.text_field :name %>
			</p>

			<p>
				<%= f.submit %>
			</p>

		<% end %>

	Add the reference tag in the view which you would like to connect to the reference form: 

	<%= render 'form' %>

	*Note: You can do this for things that aren’t forms as well :)

	Next! You need to be able to post this information back to the database…

	  def update
	    @house = House.find(params[:id])
	    @house.update(house_params)
	    redirect_to house_path
	  end

	  private
	  def house_params
	    params.require(:house).permit(:name)
	  end

	*Note: You need to add the private method because only the controller should be able to modify data

14. Create the Create Page: 
--

	— New Page Process —

	Identify desired path:

	new_house GET    /houses/new(.:format)          houses#new

	1. Create view under the views >> controllerName folder using “new.html.erb” as the name of the document

	2. Create the action inside the controller file >> 
		def new
			code goes here
		end

	3. Modify the view, so that it reflects whatever you want it to return in the HTML for that particular route

	— End New Page Process —

	1. Link the view to the form page
	2. IMPORTANT - need to create a 2nd method in the controller for actually creating your new object instance

	— New Page Process —

	Identify desired path:

	POST   /houses(.:format)              houses#create

	1. This step is not necessary here, you will redirect instead Create view under the views >> controllerName folder using “new.html.erb” as the name of the document

	2. Create the action inside the controller file >> 
		def create
			code goes here
		end

	3. Modify the view, so that it reflects whatever you want it to return in the HTML for that particular route

	— End New Page Process —

	  def create
	    @house = House.create!(house_params)
	    redirect_to houses_path
	  end

	this is capturing the @house value which was generated in the form. It is then creating a new object and permanently altering the database, which is why it was banged

15. Create the delete method
--

Add this where applicable :)

def destroy
    @house = House.find(params[:id])
    @house.destroy
    redirect_to '/'
  end

<%= link_to 'Delete House', house_path(@house),
              method: :delete,
              data: { confirm: 'Are you sure?' } %>
