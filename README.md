AskAppAPI
=========

Api for android app based on ask.fm

# [v1] API Documentation #

## Conventions ##
Various conventions related to the API are documented here.

**URL Construction**

{base_url} and {version_number} are two placeholders that will be used all over this document. There values are defined below.

1. {base_url} : http://127.0.0.1/ (base url of the main API)
2. {version_number} : v1 (the current version of the API)

Values of these two will be used to generate the URLs for every API request.

```
{base_url}/{version_number}/login
eg. http://example.com/v1/login
eg. http://example.com/v1/feeds
eg. http://example.com/v1/trending
```
**POST Parameters**

All POST requests need to provided with parameters as form values. [1] (Content-Type: application/x-www-form-urlencoded)

**GET Parameters**

All the GET endpoints of the API need to be supplied with parameters in the form of the standard [query string][2].

**Response**

The response returned by all the endpoints will be in JSON. ("Content-Type": "application/json")


## Authentication ##
Basic HTTP authentication will be used for authentication right now which basically requires every request to the server to have a set of credentials as the value of the "Authorization" key.

**Structure of Header Field**
> Authentication: Basic username:password

The header field is consisting of 3 different parts. All of them are explained below.

1. Authentication - Name of the header field.
2. Basic - Type of authentication. (Basic HTTP Authentication in our case)
3. username:password - Combination of username and password seperated by a ":". The "username" will the FB User ID and "password" the FB access token in our case.


## Errors ##

The error codes raised by every endpoint are documented in the respective sections itself. Here the different keys which a response will have in the case of an error are explained.

### Sample Error JSON ###
```
{
    "error": true,
    "error_code": 1221,
    "error_message": "Facebook access token provided is invaid."
}
```

### JSON Keys Meaning ###

1. **error(boolean):**Flag signfying if the request resulted in an error not. The android app can continue with normal workflow if the value of this key is "false" but would have to take error handling measures if the value is "true".
2. **error_code (integer):**Error code. (the various error codes raised by every endpoint are explained in the respective sections of the endpoints.
3. **error_message (string):**Human readable error message.



## Endpoints ##
Various endpoints with the HTTP verb they support are explained in this section

### POST /register_user [NO AUTH REQUIRED] ###

1. Registers a new user if the user does not already exists.
2. Returns a simple success flag back if the user already exists.

#### Parameters ####

1. **fb_user_id (string):** - Facebook User ID.
2. **fb_access_token (string):** - Facebook Access Token

#### Sample Response ####

```
{
    "error": false,
    "success": true,
    "new_registration": false,
    
}
```

#### Response Meaning ####

1. **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2. **success (boolean):**Simple flag telling if the request was a success or not. (Frankly there is not practical purpose of this one with the existence of the error flag.)
3. **new_registration (boolean):**Flag telling if the current request resulted in a new user registration or the given user already existed in the system.


#### Errors Raised ####

1. **1221:**When the access token provided is not valid.
2. **1222:**When the access token provided is valid but does not match with the given user ID.
3. **500:**: Internal server error.

### GET /feed [AUTH REQUIRED] ###

1.  Returns random questions answered by the users friends

#### Parameters ####

1.  **fb_user_id (string) :** - Facebook User Id 
2.  **fb_access_token (string) :** - Facebook Access Token
3.  **skip (integar) :** - Starting point for the feed to be sent
4.  **limit (integer) :** - Number of Feeds to be sent

#### Sample Response ####

```
{
    "error": false,
    "skip": 100,
    "limit": 50,
    "count": 50,
    "results": [
      {  
        "asker_name": Alex Josh,
        "asker_pic": pic_url,
        "asker_id": askers_user_id(in system),
        "question_content": question asked to the user?? ,
        "answer_content": answer given to that question,
        "time": answer time,
        "score": like count,
        "replier_pic": pic_url,
        "replier_name": John Doe,
        "replier_id": replier_user_id,
      },{...},{...}]
}
```
#### Response Meaning ####

1. **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2. **skip (integer):**Value of the skip parameter provided.
3. **limit (integer):**Value of the limit parameter provided.
4. **result (array):**Array of feeds to be shown to the user.
5. **asker_name (string):**Name of the person who asked the question.
6. **asker_pic (string):**Url of the picture of the asker.
7. **asker_id (string):**Id of the asker.
8. **question_content (string):**question asked to the user.
9. **answer_content (string) :** answer given to that question.
10. **time (string):**answer time.
11. **score (integer):**like count.
12. **replier_pic (string):**Url of the picture answered the question.
13. **replier_name (string):** Name of the replier.
14. **replier_id (string):**Id of the replier.
15. **count (integer):**The number of feeds in result

### GET /trending [AUTH Required] ###

1.  A similar response to that of feed will be generated except that the result will contain the questions and answers belonging to any random people not followed by or following the logged in user.

### GET /popular [AUTH Required] ###

#### Parameters ####

1.  **fb_user_id (string) :** - Facebook User Id.
2.  **fb_access_token (string) :** - Facebook Access Token.
3.  **skip (integar) :** - Starting point for the response to be sent.
4.  **limit (integer) :** - Number of responses to be sent.

#### Sample Response ####
  
```
{
    "error": false,
    "skip": 100,
    "limit": 50
    "count": 50,
    "result": [
          {
            "user_id": id_string,
            "user_name": Alex Josh
            "user_pic": pic_url,
            "ans_count": 100,
            "likes_count": 455,
            "followers_count": 160,
          },{...},{...},]
}
```  

#### Response Meaning ####

1. **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2. **skip (integer):**Value of the skip parameter provided.
3. **limit (integer):**Value of the limit parameter provided.
4. **result (array):**Array of feeds to be shown to the user.
5. **user_id (string):**Id of the user.
6. **user_name (string):**Name of the user.
7. **user_pic (string):**Url of the picture of the user.
8. **ans_count (integer):**Number of answers given by the user.
9. **likes_count (interger):**Total number of likes on all answers given by the user.
10. **followers_count (integer):**Total number of people followning the user.
11. **count (integer):**The number of feeds in result

### GET /user-profile [AUTH Required] ###

1.  Returns the bio of the user that the logged-in user wants to see.

#### Parameters ####

1.  **fb_user_id (string) :** - Facebook User Id.
2.  **fb_access_token (string) :** - Facebook Access Token.
3.  **other_user_id (string) :** - User Id of the other user whose profile has to be seen.

#### Sample Response ####

```
{
    "error": false,
    "user_id": Id_string,
    "user_name": Alex Josh,
    "user_pic": pic_url,
    "ans_count": 2345,
    "likes_count": 43435,
    "followers_count": 2133,
    "count": 50,
    "answers": [
          {
            "asker_name": Alex Josh,
            "asker_pic": pic_url,
            "asker_id": askers_user_id(in system),
            "question_content": question asked to the user?? ,
            "answer_content": answer given to that question,
            "time": answer time,
            "score": like count,
            "replier_pic": pic_url,
            "replier_name": John Doe,
            "replier_id": replier_user_id,
          },{...},{...}]
            
}
```
#### Response Meaning ####
  
1. **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2. **user_id (string):**Id of the user.
3. **user_name (string):**Name of the user.
4. **user_pic (string):**Url of the picture of the user.
5. **ans_count (integer):**Number of answers given by the user.
6. **likes_count (interger):**Total number of likes on all answers given by the user.
7. **followers_count (integer):**Total number of people followning the user.
8. **answers (array):**This array will contain the answers given by the user visited.
9. **count (integer):**The number of feeds in result

### GET /followers ; GET /following [AUTH Required] ###
  
1. */followers* - Returns the list of people following the logged-in user
2. */following* - Returns the list of people being followed by the logged-in user

#### Parameters ####

1.  **fb_user_id (string) :** - Facebook User Id.
2.  **fb_access_token (string) :** - Facebook Access Token.
3.  **skip (integar) :** - Starting point for the response to be sent.
4.  **limit (integer) :** - Number of responses to be sent.

#### Resopnse Sample ####

```
{
    "error": false,
    "skip": 100,
    "limit": 50,
    "count": 50,
    "result": [
          {
            "user_id": id_string,
            "user_name": Alex Josh
            "user_pic": pic_url,
            "ans_count": 100,
            "likes_count": 455,
            "followers_count": 160,
          },{...},{...},]
}
```  
#### Response Meaning ####

1. **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2. **skip (integer):**Value of the skip parameter provided.
3. **limit (integer):**Value of the limit parameter provided.
4. **result (array):**Array of feeds to be shown to the user.
5. **user_id (string):**Id of the user.
6. **user_name (string):**Name of the user.
7. **user_pic (string):**Url of the picture of the user.
8. **ans_count (integer):**Number of answers given by the user.
9. **likes_count (interger):**Total number of likes on all answers given by the user.
10. **followers_count (integer):**Total number of people followning the user.
11. **count (integer):**The number of feeds in result

### GET /questions [AUTH Required] ###

1.  Returns the list of questions asked to the logged-in user.
2.  Also shows the system generated questions that he can answer.

#### Parameters ####

1.  **fb_user_id (string) :** - Facebook User Id.
2.  **fb_access_token (string) :** - Facebook Access Token.
3.  **skip (integar) :** - Starting point for the response to be sent.
4.  **limit (integer) :** - Number of responses to be sent.

#### Response Sample ####

```
{
    "error": false,
    "skip": 100,
    "limit": 50,
    "count": 50,
    "questions": [  
        {
              "question_id": Id_string,
              "question_content": question asked to the logged-in user,
              "question_time": 23:00 4,23,2013,
              "asker_id": Id_string
              "asker_name": Alex Josh,
              "asker_pic": pic_url,
        },{...},{...},]
}
```

#### Response Meaning ####

1. **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2. **skip (integer):**Value of the skip parameter provided.
3. **limit (integer):**Value of the limit parameter provided.
4. **result (array):**Array of feeds to be shown to the user.
5. **count (integer):**The number of feeds in result.
6. **questions (array):**Array of questions asked to the logged-in user.
7. **question_id (string):**Id of the question asked
8. **question_content (string):**Question asked to the logged-in user.
9. **question_time (string):**Time when the question is asked. 
10. **asker_id (string):**Id of the person who asked the question.
11. **asker_name (string):**Name of the asker.
12. **asker_pic (string):**URL of the picture of the asker.

### GET /random-question [AUTH Required] ###

1.  Returns a question generated by the system to the user.

#### Parameters ####

1.  **fb_user_id (string) :** - Facebook User Id.
2.  **fb_access_token (string) :** - Facebook Access Token.

#### Sample Response ####

```
{
    "error": false,
    "question_id": Id_string,
    "question_content": question asked to the logged-in user,
    "question_time": 23:00 4,23,2013,
}
```
#### Response Meaning ####

1. **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2. **question_id (string):**Id of the question asked
3. **question_content (string):**Question asked to the logged-in user.
4. **question_time (string):**Time when the question is asked.

### POST /ask-question [AUTH Required] ###

1.  Used to ask a new question to a user.
2.  Returns the question id if successfully posted.

#### Parameters ####

1.  **fb_user_id (string) :** - Facebook User Id.
2.  **fb_access_token (string) :** - Facebook Access Token.
3.  **other_user_id (string) :** - User Id of the person to whom the question is asked.
4.  **anounimity (boolean) :** - To ask the question anonymously or not.

#### Sample Response ####

```
{
    "error": false,
    "question_id": Id_string,
}
```

#### Response Meaning ####

1. **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2. **question_id (string):**Id of the question asked.

### POST /answer [AUTH Required] ###

1.  Used to post an answer to a question asked by teh user.
2.  Return Answer_id if successfully posted

#### Parameters ####

1.  **fb_user_id (string) :** - Facebook User Id.
2.  **fb_access_token (string) :** - Facebook Access Token.
3.  **question_id (string):**Id of the question asked.
4.  **answer_content (string):**Content of the answer given.

#### Sample Response ####

```
{
    "error": false,
    "answer:id": Id_string,
}
```
#### Response Meaning ####

1.  **error (boolean):**Flag indicating if the request resulted in an error or not. If the value is 'true' the response will also have other keys explained in the errors section.
2.  **question_id (string):**Id of the answer.
