
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
| `username`      | `String` | Username **Required**.|
| `password`      | `String` | Password **Required**.  |

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
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/forgotpassword/verify
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
|      | `"coverImage" : <Link to user's cover image> ` | | 
|      | `"coverImageCloudinary_id" : <Cover image's id (in the database)> ` | | 
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
| `200`      | `"message" : "Cover image uploaded successfully"` | Photo uploaded. |
| `400`      | `"message": "Failed to upload"` | Failed to upload photo. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Upload cover image for user's profile

```http
  PATCH https://virtual-learn-api.herokuapp.com/api/v1/users/uploadcoverimage
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
            "notificationIcon" : <String - URL of notification icon>,
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

#### Get all offers

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/
```
OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` <Array of objects. Each object is an offer ` | In the format given below. |
|`500`|`"message" : "Internal Server Error"`| Server error |

    [
        {
            "_id": "<String - Note this ID to delete this offer>",
            "imageUrl": "<String>",
            "headline": "<String>",
            "description": "<String - Empty in case no description is given>",
            "createdAt": "<String>",
            "__v": 0
        }
    ]

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
  POST https://virtual-learn-api.herokuapp.com/api/v1/searches/getcoursebyname
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
  POST https://virtual-learn-api.herokuapp.com/api/v1/searches/getallincategory
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `category`      | `String` | Mention ID of the Category **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>  ` | All courses under the given category will be displayed. |
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Get courses of multiple categories with multiple chapter selection

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/searches/getcoursebymultiplefilters
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `category`      | `Array of String. Each element is category ID` | Mention IDs of all the categories required.|
| `chapter`      | `Array of String. Each element specifies number of chapters ` | Mention the required number of chapters.|


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
| `200`      | ` "data" : <array of category objects>  ` | Category object will be of as given below. |
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "_id": <ID of the category>,
        "name": <Category Name>,
        "categoryImageUrl": <URL of the category icon>,
        "searchFrequency" : <Number - Number of times this category has been searched>
        "__v": 0
    }

#### Get top searched categories

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/searches/topcategories
```
 INPUT FORMAT
 -
 Not Needed

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "data" : <array of category objects>  ` | Categories with Search Frequency in decending order. |
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "_id": <ID of the category>,
        "name": <Category Name>,
        "categoryImageUrl": <URL of the category icon>,
        "searchFrequency" : <Number - Number of times this category has been searched>
        "__v": 0
    }

#### Get course details by ID

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/searches/id
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
        "courseImageUrl" <String - course image or thumbnail>,
        "category": {
             "_id": <String - Category ID>,
            "name": <String - Category name>,
            "categoryImageUrl": <String - Category icon or image>,
            "__v": 0
        },
        "subcategories" : <Array - Subcategories>
        "courseContent": {
            "chapter": <Number - Number of chapters>,
            "lesson": <Number - Number of lessons>,
            "assignmentTest": <Number - Number of tests>,
            "totalLength": <String - Time required for entire course to complete>
        },
        "chapters": [
                "chapterID": {
                "_id": <String - Chapter ID>,
                "name" : <String - Name of the chapter>,
                "order" : <Number - Chapter Number>,
                "videos": [
                    {
                        "videoID": [
                            {
                                "_id": <String - Video ID>,
                                "name": <String - Name of the video>,
                                "videoImageUrl": <String - Thumbnail of the video>,
                                "order" : <Number - Video Number>,
                                "url": <String - Url of the video>,
                                "timeDuration" <String - Time required to complete video>
                                "__v": 0
                            },
                            {
                                "_id": <String - Video ID>,
                                "name": <String - Name of the video>,
                                "videoImageUrl": <String - Thumbnail of the video>,
                                "order" : <Number - Video Number>,
                                "url": <String - Url of the video>,
                                "timeDuration" <String - Time required to complete video>
                                "__v": 0
                            }
                        ]
                    }
                ],
                "questionnaire": {
                        "questionID": {
                                "_id": <String - Question ID>,
                                "name" <String - Name of the test>,
                                "numberOfQuestions" : <Number - Number of questions in the test>,
                                "timeDuration" : <String - Time duration of the test>,
                                "questionAnswers" [
                                    {
                                        "order" : <Number - Question Number>,
                                        "question": <String - The Question>,
                                        "options": <Array - Each element in the array is an option>,
                                        "answer": <String - Answer to the question>,
                                    },
                                    {
                                        "order" : <Number - Question Number>,
                                        "question": <String - The Question>,
                                        "options": <Array - Each element in the array is an option>,
                                        "answer": <String - Answer to the question>,
                                    }
                                ],
                                "__v": 0
                               
                            },
                        
                    },
                    "numberOfVideos": <Number - Number of videos>,
                    "numberOfQuestions": <Number - Number of questions>,
                    "__v": 0
                },
                
            }
        ],
        "overview": {
            "_id": <String - Overview ID>,
            "shortDescription": <String - Short Description>,
            "previewVideoUrl": <String - Url of the video>,
            "longDescription": <String - Description of the course>,
            "courseIncludes": [
                {
                    "iconUrl": <String - Icons url>,
                    "description": <Stirng- Description>
                },
                {
                    "iconUrl": <String - Icons url>,
                    "description": <Stirng- Description>
                },
                {
                    "iconUrl": <String - Icons url>,
                    "description": <Stirng- Description>
                }
            ],
            "whatYouWillLearn": [
                {
                    "iconUrl": <String - Icons url>,
                    "description": <Stirng- Description>
                },
                {
                    "iconUrl": <String - Icons url>,
                    "description": <Stirng- Description>
                }
            ],
            "requirements": [
                <String - Array of requirements> 
            ],
            "instructorName": <String - Name of the instructor>,
            "instructorImageUrl": <Sring - Author's Image>,
            "instructorTitle": <Sring - Author's Title>,
            "instructorDescription": <String - Author's Description>,
            "__v": 0
        },
        
        "__v": 0
    }
    }

| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `404`      | `"message" : "Course not found"  ` | Category unavailable. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Enroll in Course

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/enroll
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `courseID`      | ` String` | ID of the course  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"message" : "Enrolled in course successfully"` | Enrolling successful. |
| `409`      | `"message": "Already enrolled in course"` | User has already enrolled in course. |
|`404` |  ` "message": "Course Not Found" ` | Wrong course ID |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Get all enrolled courses

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/users/getenrolledcourses
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"coursesEnrolled" : [ {}, {} ]` | All courses the user has enrolled in, res in the format given below. |
|`404` |  ` "message": "User has not enrolled in any course" ` | User has not enrolled in any of the courses. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "coursesEnrolled": [
            {
                "_id": <ID of course status>,
                "userId": <ID od user>,
                "courseID": <ID of course>,
                "chapterProgressData": <chapterProgress empty if the user has not saved any cprogress>,
                "__v": 0
            }, 
            {
                "_id": <ID of course status>,
                "userId": <ID od user>,
                "courseID": <ID of course>,
                "chapterProgressData": <chapterProgress empty if the user has not saved any cprogress>,
                "__v": 0
            }
        ]
    }

#### Get course progress(only to check if user has enrolled or not)

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/getcourseprogress
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `courseID`      | ` String` | ID of the course  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"progressData" : {} ` | Progress of the course in the chapterProgressData as an array. |
| `409`      | ` "message" : " User has not enrolled in the course"` | User has not enrolled in course. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |


### Update video progress
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/updatevideoprogress
```
| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `courseID`      | ` String` | ID of the course  **Required**.|
As the format below
    {
        "courseID": <String>,
        "chapterID" : <String>,
        "videoID" : <String>,
        "videoOrder" : <Number - Order of the video>,
        "progressRate" : <Number - (pass only numbers)percentage completed>,
        "watchedTill" : <String>
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `old data(can be ignored) ` | Status saved. |
| `403`      | ` message: "You are not enrolled in this course"` | User has not enrolled in course. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

### Update questionnaire progress/result
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/updatequestionaireprogress
```
| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `courseID`      | ` String` | ID of the course  **Required**.|
As the format below
    {
        "courseID": <String>,
        "chapterID" : <String>,
        "questionaireID" : <String>,
        "approvalRate" : <Number - in percentage>,
        "right" : <Number - Number of right answers>,
        "wrong" : <Number - Number of wrong answers>
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `old data(can be ignored) ` | Status saved. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |


### Get the course progress
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/getallprogress
```
| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `courseID`      | ` String` | ID of the course  **Required**.|


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | <Object>` | Output as the format given below. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "videos": [
            {
                "_id": "<String>",
                "userId": "<String>",
                "courseID": "<String>",
                "chapterID": "<String>",
                "videoID": "<String>",
                "videoOrder": 1,
                "progressRate": 100,
                "watchedTill": "<String>",
                "__v": 0
            },
            {
                "_id": "<String>",
                "userId": "<String>",
                "courseID": "<String>",
                "chapterID": "<String>",
                "videoID": "<String>",
                "videoOrder": 2,
                "progressRate": 100,
                "watchedTill": "<String>",
                "__v": 0
            },
            {
                "_id": "<String>",
                "userId": "<String>",
                "courseID": "<String>",
                "chapterID": "<String>",
                "videoID": "<String>",
                "videoOrder": 3,
                "progressRate": 100,
                "watchedTill": "5 min",
                "__v": 0
            }
        ],
        "questionaire": [
            {
                "_id": "<String>",
                "userId": "<String>",
                "courseID": "<String>",
                "chapterID": "<String>",
                "questionaireID": "<String>",
                "approvalRate": 80,
                "right": 8,
                "wrong": 2,
                "__v": 0
            }
        ],
        "chapters": [
            {
                "_id": "<String>",
                "userId": "<String>",
                "courseID": "<String>",
                "chapterID": "<String>",
                "chapterProgressRate": 100,
                "__v": 0,
                "chapterApprovalRate": 80
            },
            {
                "_id": "<String>",
                "userId": "<String>",
                "courseID": "<String>",
                "chapterID": "<String>",
                "chapterProgressRate": 80,
                "__v": 0
            }
        ],
        "course": {
            "_id": "<String>",
            "userId": "<String>",
            "courseID": "<String>",
            "joinedOn": "Wed Feb 02 2022 11:04:34 GMT+0530 (India Standard Time)",
            "__v": 0,
            "courseCompletionRate": 100,
            "completedOn": "Wed Feb 02 2022 13:45:32 GMT+0530 (India Standard Time)",
            "courseApprovalRate": 80
        }
    }

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
| `uvideoImageUrlrl`      | `String` | Url of the video thumbnail  **Required**.|
| `order`      | `Number` | Order of the video  **Required**.|
| `url`      | `String` | Url of the video  **Required**.|
| `timeDuration`      | `String` | Time duration of the video  **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` <Video Schema> ` | Video is added. Video details will be displayed. Make a note of the id. |
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
| Input as example given below |

        {
            "name" : <String - Name of the test>,
            "numberOfQuestions" : <Number -Number of questions in the test>,
            "timeDuration" : <String - TIme Duration of the test>,
            "questionAnswers": [
                {
                    "order" : <Number - Question number>,
                    "question" : <String - Question>",
                    "options" : [
                        <String - Array of options>,
                        <String - Array of options>,
                        <String - Array of options>,
                        <String - Array of options>                        
                        ],
                    "answer" : <String - Answer>
                },
                {
                    "order" : <Number - Question number>,
                    "question" : <String - Question>",
                    "options" : [
                        <String - Array of options>,
                        <String - Array of options>,
                        <String - Array of options>,
                        <String - Array of options>                        
                        ],
                    "answer" : <String - Answer>
                }
            ] 
        }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` <Questionaire Schema>  ` | Make a note of the id. |
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
            "name" : <String - Name of the chapter>,
            "order" : <Number - Chapter Number>,
            "videos": {

                "videoID" : [
                    <String - ID of the first video>,
                    <ID of the second video>,

                ]
        
            },
            "questionnaire": {
                "questionID" : <String - ID of the questionnaire>
                
            },
            "numberOfVideos": <Number - Number of videos>,
            "numberOfQuestions": <Number - Number of Questions>
        }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `<chapter schema> ` | Make a note of the id. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Add Overview
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/courses/addoverview
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| Input as the given example. 


    {
        "shortDescription" : <String - Short description of the course>,
        "previewVideoUrl" : <String - Url of the preview video>,
        "previewVideoThumbnail" : <String - URL of preview video thumbnail>,
        "longDescription" : <String - About the course in detail>,
        "courseIncludes" : [
            {
                "iconUrl" : <String - URL of the icon>,
                "description" : <String - Point>
            },
            {
                "iconUrl" : <String - URL of the icon>,
                "description" : <String - Point>
            },
            {
                "iconUrl" : <String - URL of the icon>,
                "description" : <String - Point>
            }
        ],
        "whatYouWillLearn" : [
            {
                "iconUrl" : <String - URL of the icon>,
                "description" : <String - Point>
            },
            {
                "iconUrl" : <String - URL of the icon>,
                "description" : <String - Point>
            }

        ],
        "requirements" : [
            <String - Point>,
            <String - Point>,
            <String - Point>

        ],
        "instructorName" : <String - Name of the instructor>,
        "instructorImageUrl":  <String - URL of the instructor's image>,
        "instructorTitle" :  <String - Title of the instructor>,
        "instructorDescription" :  <String - About instructor in detail>
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `<overview schema> ` | Make a note of the id. |
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
        "courseImageUrl" : <String - Url of the course Image>,
        "category" : <String - Category ID here>,
        "subcategories" [
            <String - Subcategory 1>,
            <String - Subcategory 2>,

        ],
        "courseContent" : {
            "chapter" : <Number - Number of chapters>,
            "lesson" : <Number - Total number of lessons>,
            "assignmentTest" : <Number - Total number of tests>,
            "totalLength" : <String - Total time required for the course>
        },
        "chapters" : [ 
            {
                "chapterID" : <String - Mention ID of the chapter that we had previously noted>
            },
            {
            "chapterID" : <String - Mention ID of the chapter that we had previously noted>
            }   
          
        ],
        "overview" : <String- ID of the overview>
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` <Course Schema>  ` | Course is now added successfully |
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Add category
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/courses/addcategory
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| Input as the given example. 


        {
            "name" : <String - Name of the category>,
            "categoryImageUrl" : <String - URL of the category icon>
        }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `<category schema> ` | Make a note of the id. |
|`500`|`"message" : "Internal Server Error"`| Server error |

#### Add offer
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| Input as the given example. 


    {
        "imageUrl":"<String>",
        "headline":"<String>",
        "description":"<String>"
        
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `<Offer Object> ` | Make a note of the id in case you want to delete. |
|`500`|`"message" : "Internal Server Error"`| Server error |


#### Delete offer
```http
  DELETE https://virtual-learn-api.herokuapp.com/api/v1/
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Admin auth key |

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| Input as the given example. 


    {
        "_id" : "<String>"
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"deletedCount" : <Number> ` | Number will be 1 in case it is deleted. |
|`500`|`"message" : "Internal Server Error"`| Server error |
