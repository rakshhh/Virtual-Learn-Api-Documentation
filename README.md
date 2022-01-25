
# Virtual learn API




## API Reference

#### Register user - Send OTP to given phone number

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/signup/otp
```
 INPUT FORMAT
| Body (raw)(JSON) | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `number` | `String` | 10 digit phone number beginning with contry code (eg. +91) **Required**. |

OUTPUT FORMAT (Output will be in the form of JSON object)
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message": "Otp sent"` | OTP is sent to the phone number provided |
|`409`|`"message": "User already registered"`| User can reset password using phone number or login using username|
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Register user - Verify phone number

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/signup/verify
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number provided before **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Phone Number Verified"` | User can now be registered using this number.|
|`401`| `"message" : "Incorrect OTP"` | OTP entered is wrong |
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Register user - Create account using user details (Will receive authentication key after registration)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/signup/register
```
 INPUT FORMAT

| Body | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `number` | `String` | **Required**. |
| `fullname` | `String` | **Required**. |
| `username` | `String` | **Required**. |
| `email` | `String` | **Required**. |
| `password` | `String` | **Required**. |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
|`200`| ` "message": "User registered successfully"` | Authentication key will be generated(to be used when required).|
||                                    `"token": <AUTH KEY>`| |
|`401`| ` "message" : 'User registration failed' ` | User is not registered |
| `408`      | ` "message": "Session expired"` | Request timeout, go back to user registration(send otp).|
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Login (Authentication key will be received)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/login
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number provided before **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message": "Login Successful"` | Username and password entered exists and matches. Auth Key is generated.|
|| `"token": <AUTH KEY> ` | |
| `404`      | `"message": "User dosen't exist"` | Username entered does not exist. Mention login failed. |
| `401`      | ` "message": "Login failed"` | Password entered is wrong. Mention login failed. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Forgot Password (on login page) (Otp will be sent to the given number if the user has registered using the same before)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/forgotpassword/otp
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Otp sent" ` | OTP is sent to the given number.|
| `404`      | `"message" : "User does not exist"` | Phone number was not used to register. Go to user registration.|
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Verify OTP (for forgot password (on login page))

```http
  POSThttps://virtual-learn-api.herokuapp.com/api/v1/users/forgotpassword/verify
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|
| `otp`      | `Number` | OTP received on the phone number **Required**.  |


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Phone Number Verified"` | User can now reset password using this number.|
|`401`| `"message" : "Incorrect OTP"` | OTP entered is wrong |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Reset password (Possible if otp is correct)

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/users/forgotpassword/resetpassword
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `number`      | `String` | Phone number that was used to create the account **Required**.|
| `password`      | `String` | New password **Required**.  |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "message" : "Password changed successfully"` | Go to login. Login with username and new password. |
| `408`      | `"message": "Session expired"` | User took too long to respond. Resend otp and verify.|
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Get user details

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/users/displayprofile
```
 INPUT FORMAT

| Header (API KEY) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200 (OK)`      | `"number" : <User's phone number> ` | User details  are displayed.| 
|      | `"fullname" : <User's full name> ` | | 
|      | `"username" : <User's username> ` | | 
|      | `"email" : <User's mail id> ` | | 
|      | `"occupation" : <User's Occupation> ` | | 
|      | `"dateOfBirth" : <User's Date Of Birth> ` | | 
|      | `"gender" : <User's Gender> ` | | 
|      | `"twitterLink" : <Link to user's twitter profile> ` | | 
|      | `"facebookLink" : <Link to user's facebook profile> ` | | 
|      | `"image" : <Link to user's profile picture> ` | | 
|      | `"_id" : <User's id (in the database)> ` | | 
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Update user details

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/users/updateprofile
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `fullname`      | `String` | Full name |
| `occupation`      | `String` | Occupation |
| `dateOfBirth`      | `String` | Date of birth |
| `gender`      | `String` | Gender |
| `twitterLink`      | `String` | Link to user's Twitter profile |
| `facebookLink`      | `String` | Link to user's Facebook profile |
| `email`      | `String` | User's email ID |

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Profile updated successfully"` | Profile updated. |
| `400`      | `"message": "Failed to update"` | Profile is not updated. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Upload user profile picture

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/users/uploadimage
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `image`      | `file` | Image in jpeg/png format  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Picture uploaded successfully"` | Photo uploaded. |
| `400`      | `"message": "Failed to upload"` | Failed to upload photo. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Change password providing current password

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/users/changepassword/oldpassword
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `oldpassword`      | `String` | User's current password  **Required**.|
| `newpassword`      | `String` | New password  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message": "Password changed successfully"` | Password changed. |
| `400`      | `"message": "Wrong Password, Try forgot password"` | Old password is incorrect. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Get all notifications

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/users/notifications
```
| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` Array of all notifications ` | Empty array in case there are no notifications. In case there are then it will bw displayed in the following format given below. |
|`500`|`"message" : "Internal Server Error"`| Server error |

    [
        {
            "_id": <String - Notification ID>,
            "user_id": <String - User ID>,
            "notification": <Notification(password change, addition of new course, enrolled course, any part of course completion)>,
            "createdAt": <String - Date at which the notification was created eg: "Mon Jan 24 2022 14:55:18 GMT+0530 (India Standard Time)" > ,
            "__v": 0
        }
    ]

#### Delete all notifications

```http
  DELETE https://virtual-learn-api.herokuapp.com/api/v1/users/notifications
```
| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "message": "Notifications cleared" ` | Notifications are deleted. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Get all courses

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/searches/
```
 INPUT FORMAT
 - 
 Not Needed

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>` | All courses available will be displayed. In case no courses are available then it will return empty array |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Get a course by name

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/searches/getcoursebyname
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `name`      | `String` | Name of the course to be searched  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>  ` | All courses with the given name will be displayed. Empty array if no course if found|
|`500`|`"message" : "Internal Server Error"`| Server error |



#### Get all courses in a given category

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/searches/getallincategory
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `category`      | `String` | Category required  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>  ` | All courses under the given category will be displayed. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Get all categories available

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/searches/categories
```
 INPUT FORMAT
 -
 Not Needed

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "data" : <array of category names>  ` | All unique category names. |
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Get course details by ID

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/searches/id
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `id`      | `String` | Course ID  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "data" : <object of course details>  ` | Course details in the given format. |

    {
    "data": {
        "_id": <String - Course ID>,
        "name": <String - Name of the course>,
        "category": <String - Category of the course>,
        "courseContent": {
            "chapter": <Number - Number of chapters>,
            "lesson": <Number - Number of lessons>,
            "assignmentTest": <Number - Number of tests>,
            "totalLength": <String - Time required for entire course to complete>
        },
        "chapters": {
            "chapterName": <String - Chapter Name>,
            "chapterID": {
                "_id": <String - Chapter ID>,
                "videos": [
                    {
                        "videoID": [
                            {
                                "_id": <String - Video ID>,
                                "name": <String - Name of the video>,
                                "url": <String - Url of the video>,
                                "__v": 0
                            },
                            {
                                "_id": <String - Video ID of another video in this chapter>,
                                "name": <String - Name of the video>,
                                "url": <String - Url of the video>,
                                "__v": 0
                            }
                        ]
                    }
                ],
                "questionnaire": [
                    {
                        "questionID": [
                            {
                                "_id": <String - Question ID>,
                                "question": <String - The Question>,
                                "options": <Array - Each element in the array is an option>,
                                "answer": <String - Answer to the question>,
                                "__v": 0
                            },
                            {
                                "_id": <String - Question ID of another question in this chapter>,
                                "question": <String - The Question>,
                                "options": <Array - Each element in the array is an option>,
                                "answer": <String - Answer to the question>,
                                "__v": 0
                            }
                        ]
                    }
                ],
                "numberOfVideos": <Number - Number of videos>,
                "numberOfQuestionnaires": <Number - Number of questions>,
                "__v": 0
            }
        },
        "__v": 0
    }
    }

| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `404`      | `"message" : "Course not found"  ` | Category unavailable. |
|`500`|`"message" : "Internal Server Error"`| Server error |




# For ADMIN

#### Add video
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/courses/addvideo
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `name`      | `String` | Name of the video  **Required**.|
| `url`      | `String` | Url of the video  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` name, url, id, __v  ` | Video is added. Video details will be displayed. Make a note of the id. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Add question
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/courses/addquestion
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `question`      | `String` | Question  **Required**.|
| `options`      | `Array` | Array of options  **Required**.|
| `answer`      | `String` | Answer  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` question, options, answer, id, __v  ` | Make a note of the id. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Add chapter
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/courses/addchapter
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| Input as the given example. 


    {
        "videos": {

            "videoID" : [
                <The ID of the first video that we had noted here>,
                <Next video's ID here>"
            ]
    
        },
        "questionnaire": {
            "questionID" : [
                <The ID of the first question we had noted here>,
                <Next questions's ID here>"
            ]
        },
        "numberOfVideos": <Number - Number of videos>,
        "numberOfQuestionnaires": <Number - Number of Questions>
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `<chapter details will be displayed> ` | Make a note of the id. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Add Course
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/courses/addcourse
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| Input as the given example. 

    {
        "name" : <String - Course Name here>,
        "category" : <String - Category name here>,
        "courseContent" : {
            "chapter" : <Number - Number of chapters>,
            "lesson" : <Number - Total number of lessons>,
            "assignmentTest" : <Number - Total number of tests>,
            "totalLength" : <String - Total time required for the course>
        },
        "chapters" : {
            "chapterName" :<String - Name of the Chapter> <Add chapters in series here>,
            "chapterID" : <String - Mention ID of the chapter that we had previously noted>
        }
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` Course details will be displayed  ` | Course is now added successfully |
|`500`|`"message" : "Internal Server Error"`| Server error |

