# Todo
To run
```npm run dev```

This Todo app will use Vuejs for client JS and some routing.
For Development I am using parcel-bundler and will build for production. 

There will be almost no changes between development mode and production, this is a DEMO only and not to be trusted with important or private information. This should only be run locally for your safety.

## Devel Readme

### Outline

This todo app is going to be a show of user authentication and database usage.
I have so many questions about how I should do this, what is right? The user authentication and routing. 
I cant say what is right but I know I have come up with quite a few ways to tackle this issue so in this case I will use the most current one I have used. There will be two frontend web apps, one for a user who is not logged in and one for a user that is logged in.

If a user is not logged in they will only have access to the app with..
1. login
2. register
3. landing
4. error
5. about/contact

After the user is authenticated they will have access to the main app that brings them to their dashboard where they can create, edit, favorite, share and delete todo list. Also a page for deleting their account and all data.

#### Register
When the user visits the app for the first time they will have the options to login or register.
The user will have the option to register via local, google or github.

#### Scope
1. email[s]
2. image[user image]
3. age[birthday]

Each users account will be tied to there email ensuring one account per email.
Once the user has created their account their session will be active on that device/browser and will not need to be gotten again unless they logout.

Accounts can be synced to other services after registration.

Local registrants must confirm via email. Which will be sent simple from my email for this example, once the email has been confirmed the user will have access to their account.

#### Login
If a user has registered their account they can login using the same service as they used to register or any they have connected.
* url = '/login' method = POST
* Server side check for email and password
* Error = res.json({status: 'BAD', message: 'Please Enter An Email and Password'})

#### Dashboard
The heart of the small app will be the user dashboard where all of the todo list are made. 
The layout of the dashboard will be mobile first design with desktop as well. Simple layout with some user information, a picture, heading and about me section.
Then an area for the button that will make a new list.
A section for current lists titles they can be clicked on to edit, share or delete.

* for desktop the list editor may be a modal?

#### Sharing
The user will have the option to share there list to google+, twitter and facebook as user post.

#### Routing
A lot of view routes can be handled via JavaScript front-end routing. Each app will have its own routes.
This  will keep the server router simple, if auth is false app one, if true then both one and two.
For the ajax calls there will be a check for auth and correct user to try and prevent hacking.

#### Database
I will use mongodb for this project with mongoose. 
Schemas will be 
1. User
2. Todo

##### User
{
    _id: ObjectId,
    email: String,
    imageUrl: String,
    todos: Array: Todo,
    account: Object
}
account {
    created: Date,
    google: String,
    github: String,
    local: String
}

##### Todo
{
    _id: ObjectId,
    owner: ObjectId: User,
    title: String,
    items: Array,
    created: Date,
    lastEdit: Date
}

This is very simple but is plenty for this small app.

#### Sessions
The sessions will be handled with express-session and stored with connect-mongodb-sessions
Sessions do not expire, user must logout. I may change this,up in the air about what to do here.

#### Encryption
Encryption will be used for local passwords. I will use bcrypt for this.

## API routes

### POST

#### Error Handling For Post
There will be a status object that will say its a bad call and a message.
There will be a modal or a page that will display the message letting the user know something is wrong and something they could try to fix it. 
* All Post responses will be in JSON

#### Create New List
* url = '/create-new-list'
* A new todo will be created with the given title or "Untitled", the users id and then be saved to the DB.
* The sessions current todo will be set to the new todo's id.
* The response will be the id of the new todo.
* This response will trigger a modal or app route to the Todo Editor with the new list loaded after /get-todo-data is called.

#### Get List Of User's Todo List
* url = '/todos'
* The DB will be searched for all todo's belonging to the current user and the titles, created and edit dates will be gotten and sent as response.

#### Load A Selected List
* url = '/set-current-todo'
* When the user selects a Todo from their list to edit this will be opened
* The session.currentTodo = the selected list id.
* The response will be the Todo id
* This will open the Todo editor with the selected list and call for the list.

#### Load Todo Data
* url = '/load-todo-data'
* This call will be made when the editor opens
* The session.currentTodo will be loaded from the DB
* The current sessions user will be checked against the Todo user._id
    - Error = res.json({status: 'BAD', message: 'Sorry Something Doesn't Feel Right./nMake Sure You Are Logged In.'})

#### Add New Item To Todo
* url = '/add-item'
* add item to session.currentTodo
* {
    _id: random string
    body: new item body
}
* response is {status: 'OK', message: 'New Item Added To Your List!'}
* Session user id will be checked against the current Todo owner
 - Error = res.json({status: 'BAD', message: 'Sorry Something Doesn't Feel Right./nMake Sure You Are Logged In.'})

### Delete

#### Remove Item From Todo
* url = '/remove-item'
* remove an item from session.current
* The item is removed by its _id
* Session user id will be checked against the current Todo owner
 - Error = res.json({status: 'BAD', message: 'Sorry Something Doesn't Feel Right./nMake Sure You Are Logged In.'})

#### Delete Todo
* url = '/delete-todo'
* This invokes a prompt for the user to confirm they would like to delete their todo
* drops the Todo from the database using its _id
* Session user id will be checked against the current Todo owner
 - Error = res.json({status: 'BAD', message: 'Sorry Something Doesn't Feel Right./nMake Sure You Are Logged In.'})
 