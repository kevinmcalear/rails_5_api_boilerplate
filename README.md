
####Setting up our API

  # set up a new rails app
  rails new test_api --api --database=postgresql

  # set up our database
  rake db:setup

  # set up a user table, model, controller & tests
  rails g scaffold user name:string username:string age:integer email:string

  # update our database schema & relationships
  rake db:migrate

  # oops forgot to add our api key
  rails generate migration add_api_key_to_users api_key:string

  # update our database schema & relationships
  rake db:migrate

  # run our tests
  rake test

  # create the concept of snacks
  rails generate scaffold snack name:string location:string description:string user:references

  # create favorites for users
  rails g model Favorite snack:references user:references

  # update our database schema & relationships
  rake db:migrate

  # set up has_many :snacks, through: :favorites in user model relationship

  # set up has_many :users, through: :favorites in snack model relationship

  # set up belongs_to :user & belongs_to :snack in favorite model relationship

  #add nested routes in routes.rb

  #modify snack index
  def index
      if params[:user_id]
        @snacks = User.find(params[:user_id]).snacks
      else
        @snacks = Snack.all
      end
      render json: @snacks
    end


####Add a token to our user for authentication

  before_create :set_initial_api_key

  private

  def set_initial_api_key
      self.api_key ||= generate_api_key
    end

  def generate_api_key
      SecureRandom.uuid
    end


#####Add Authentication to our controller

  before_action :authenticate

    private

    def authenticate
      authenticate_api_key || render_unauthorized
    end

    def authenticate_api_key
      api_key = request.headers['X-Api-Key']
      @auth_user = User.find_by(api_key: api_key)
    end

    def auth_user
      @auth_user
    end

    def render_unauthorized
      render json: 'Bad credentials', status: 401
    end

####Enable CORs

* enable the gem in the Gemfile
* bundle
* enable the cors.rb initializer & add your angular host
* profit  $$$


####Seed the DB

  User.create([
      {name: "Kevin McAlear", age: 24, username: "mcaleark", email: "mcaleark@amazon.com"},
      {name: "Tyler Barrington", age: 27, username: "atb", email: "atb@amazon.com"},
      {name: "Justin Bieber", age: 22, username: "beebs", email: "beebs@amazon.com"},
      {name: "Jeff Bezos", age: 22, username: "jeff", email: "sweetjeffery@amazon.com"},
      {name: "Dave Mozealous", age: 22, username: "mozealou", email: "mozealouboi@amazon.com"},
      {name: "Tony Lee", age: 22, username: "hanwool", email: "thetiger@amazon.com"},
  ])

  Snack.create([
      { name: "Bitterballen", location: "Netherlands", description: "A round shaped beef-ragout version of croquette, typically containing a mixture of beef or veal (minced or chopped), beef broth, butter, flour for thickening, parsley, salt and pepper, resulting in a thick ragout. Most recipes include nutmeg and there are also variations utilizing curry powder or that add in finely chopped vegetables such as carrot." },
      { name: "Bonda", location: "South India", description: "Various sweet and spicy versions of exist different regions. The process of making a spicy bonda involves deep-frying potato (or other vegetables) filling dipped in gram flour batter." },
      { name: "Cereal", location: "My Mom's house", description: "A food made from processed grains that is often eaten cold, usually mixed with milk (e.g. cow's milk, soy milk, rice milk, almond milk), juice, water, or yogurt, sugar, and sometimes fruit, but may be eaten dry." },
      { name: "Cokodok", location: "Dunno", description: "round in shape and tends to vary in size." },
      { name: "Crêpe", location: "France", description: "A type of very thin pancake, usually made from wheat flour (crêpes de Froment) or buckwheat flour (galettes). The word is of French origin, deriving from the Latin crispa, meaning \"curled\"." },
      { name: "Croquette", location: "France", description: "A French invention with worldwide popularity, a croquette is a small breadcrumbed fried food roll containing, usually as main ingredients, mashed potatoes and/or ground meat (veal, beef, chicken, or turkey), shellfish, fish, cheese, vegetables and mixed with béchamel or brown sauce, and soaked white bread, egg, onion, spices and herbs, wine, milk, beer or any of the combination thereof, sometimes with a filling, e.g. sauteed onions or mushrooms, boiled eggs (Scotch eggs)." },
      { name: "Doughnut", location: "global", description: "A fried dough snack popular in most parts of the world" },
      { name: "Gulha", location: "Maldives", description: "A popular snack in Maldives" },
      { name: "Khanom", location: "Thailand", description: "A snack sold by street vendors in Thailand" },
      { name: "Pakora", location: "India", description: "A fried snack (fritter) found across South Asia" },
      { name: "Pancakes", location: "Grandmas", description: "Powder mix made in the morning for breakfast with syrup" },
      { name: "Pizza", location: "Disputed", description: "Disputed. Sources state that Ancient Greece and Italy are likely. Such as mini pizza and pizza baguette, as well as pizza sold by the slice or by weight" },
      { name: "Pretzel", location: "My belly", description: "Yummy yummy numm numms in my tumm tumm!" },
      { name: "Poffertjes", location:"Netherlands", description: "A traditional Dutch batter treat" },
      { name: "Waffle", location: "Belgium", description: "A batter-based or dough-based cake cooked in a waffle iron patterned to give a characteristic size, shape and surface impression. There are many variations based on the type of iron and recipe used, with over a dozen regional varieties in Belgium alone." }
  ])


