#P7 Hangman Game API

## Set-Up Instructions:

1.  Clone this project then add to project to Google App Engine (GAE)
1.  Depending on your port number (i.e. 8080) type localhost:8080/_ah/api/explorer into browser to test endpoints

## Testing API
1.  Create a new user, using the create_user endpoint. 
2.  Use create_game to create a game. Remember to copy the
    urlsafe_key property for later use.
3.  Make a move, requires the urlsafe_key and a guess to start
    reduces the attempt_remaining if guess is wrong until game is finished
    or user guesses the answer. Game_over property is set True.
4.  Get user games, requires username and email and lists all user's
    ACTIVE games (game_over = False)
5.  Cancelling a game, requires username and game's urlsafe_key.
    Users are not permitted to remove completed games.
6.  Getting high scores endpoints, returns all scores ordered by
    attempts remaining (descending) least attempts to solve = better
7.  Getting user rankings, returns all users ordered by their performance
8.  Getting game history, requires game urlsafe_key which returns
    all the moves made during a certain game
 
 
##Game Description:
HangmanAPI is a simple guessing game. The user creates a game and has to guess the 
'answer' in a limited amount or 'attempts' which can be set by user.
'Guesses' are sent to the `make_move` endpoint which will return
an appropriate answer for the user. Each game can be retrieved 
or played by using the path parameter
`urlsafe_game_key`.

##How Score Keeping Works:
Whenever a user completes a game either by winning or losing a game
this adds 1 point to the user's wins or losses property. The user's performance
property is then calculated and displayed as win/loss ratio. (wins / total games * 100)

##Files Included:
 - api.py: Contains endpoints and game playing logic.
 - app.yaml: App configuration.
 - cron.yaml: Cronjob configuration.
 - main.py: Handler for taskqueue handler.
 - models.py: Entity and message definitions including helper methods.
 - utils.py: Helper function for retrieving ndb.Models by urlsafe Key string.

##Endpoints Included:
 - **create_user**
    - Path: 'game/create_user'
    - Method: POST
    - Parameters: name, email
    - Returns: Message confirming creation of the User.
    - Description: Creates a new User. user_name provided must be unique. Will 
    raise a ConflictException if a User with that user_name already exists.
    
 - **new_game**
    - Path: 'game/new_game'
    - Method: POST
    - Parameters: name, topic, answer, attempt_remaining
    - Returns: GameForm with initial game state.
    - Description: Creates a new Game. name provided must correspond to an
    existing user - will raise a NotFoundException if not. Not setting the attempts
    will default to 6.
     
 - **get_current_game**
    - Path: 'game/{urlsafe_game_key}'
    - Method: GET
    - Parameters: urlsafe_game_key
    - Returns: GameForm with current game state.
    - Description: Returns the current state of a game.
    
 - **make_move**
    - Path: 'game/{urlsafe_game_key}'
    - Method: PUT
    - Parameters: urlsafe_game_key, guess
    - Returns: GameForm with new game state.
    - Description: Accepts a 'guess' and returns the updated state of the game.
    If this causes a game to end, a corresponding Score entity will be created.
    
 - **get_user_games**
    - Path: 'game/get_activeGames'
    - Method: GET
    - Parameters: name, email
    - Returns: All user's active games.
    - Description: This returns all of a User's active games. 
    
 - **cancel_game**
    - Path: 'scores/user/{user_name}'
    - Method: DELETE
    - Parameters: user_name, urlsafe_game_key
    - Returns: String message confirming game deletion 
    - Description: This endpoint allows users to cancel a game in progress.
     Users are not permitted to remove completed games.
    
 - **get_high_scores**
    - Path: 'game/get_high_scores'
    - Method: GET
    - Parameters: number_of_results (optional)
    - Returns: List of scores
    - Description: Generates a list of high scores in descending order.
    
 - **get_user_rankings**
    - Path: 'game/get_user_rankings'
    - Method: GET
    - Parameters: None
    - Returns: List of all players 'name' and 'performance' ordered by 'performance'.
    - Description: Returns all players ranked by performance.(eg. win/loss ratio).

 - **get_game_history**
    - Path: 'game/get_game_history'
    - Method: GET
    - Parameters: urlsafe_game_key
    - Returns: List of a game's history
    - Description: Generates a list of a user's moves during a game

##Models Included:
 - **User**
    - Stores unique user_name and email address.
    
 - **Game**
    - Stores unique game states. Associated with User model via KeyProperty.
    
 - **Score**
    - Records completed games. Associated with User and Game model via KeyProperty.
    
##Forms Included:
 - **UserForm**
    - Representation of a user's state (name, email, activeGameKeys, wins,
    losses, performance)
 - **UserForms**
    - Returns multiple UserForm (items)
 - **GameForm**
    - Representation of a Game's state (urlsafe_key, attempts_remaining,
    game_over flag, message, user_name, topic, hidden, guesses, date).
 - **GameForms**
    - Returns multiple GameForm (items)
 - **ScoreForm**
    - Representation of a completed game's Score (game, user_name, date, won flag,
    attempts_remaining).
 - **ScoreForms**
    - Multiple ScoreForm container.
 - **CancelForm**
    - Returns multiple UserForm (items)
 - **StringMessage**
    - General purpose String container.
