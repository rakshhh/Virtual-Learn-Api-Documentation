
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
| `200 (OK)`      | `<data object and has completed object in the format given below` | User details  are displayed.| 
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "data": {
            "_id":<String - This is the ID of the user data>,
            "number": <String - Phone number of the user>,
            "fullname": <String - User's full name>,
            "username": <String - Username>,
            "email": <String - Email ID of the user>,
            "occupation" : <String - User's occupation>,
            "dateOfBirth" : <Date - This data is of type 'date' that is present in MONGODB>,
            "gender" :  <String - Gender of the user>,
            "facebookLink" : <String - Link to user's facebook profile>,
            "twitterLink" : <String - Link to user's twitter profile>,
            "image": <String - URL of user's profile picture>,
            "cloudinary_id": <String - Cloudinary ID of the profile picture>,
            "coverImage": <String - URL of user's cover photo>,
            "coverImageCloudinary_id": <String - Cloudinary ID of user's cover photo>,
            
        },
        "hasCompleted": {
            "chapters": <Number of chapters completed>,
            "courses": <Number of courses completed>,
            "test": <Number of tests completed>
        }
    }

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
            "notification": <Notification(password change, addition of new course, enrolment in new course, chapter completion, test completion, course completion)>,
            "notificationIcon" : <String - URL of notification icon will be different acc to the type of notification>,
            "createdAt": <String - Date at which the notification was created, will be in ISO format" >,
            "isRead": <Boolean - default value (during creation) is false>
        }
    ]
#### Mark all notifications as read

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/notifications
```
| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` <Update result> ` | Notifications are now marked read(i.e. isRead value is update to true). |
|`500`|`"message" : "Internal Server Error"`| Server error |


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
| `200`      | ` <Object of Array of objects. Each object is an offer> ` | In the format given below. |
|`500`|`"message" : "Internal Server Error"`| Server error |


    {
    "data":[
                {
                    "_id": "<String - Note this ID to delete this offer>",
                    "imageUrl": "<String - URL of the offer image>",
                    "headline": "<String - Headline of the offer>",
                    "description": "<String - Empty in case no description is given>",
                    "createdAt": "<String of type ISO>"
                }
        ]
    }



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

#### Get courses by a few words

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/searches/getcoursebywords
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `name`      | `String` | Any number of words **Required**.|

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>  ` | All courses which contain the words used in search will be displayed. Empty array if no course if found|
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

#### METHOD 1 - Get courses of multiple categories with multiple chapter selection

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/searches/getcoursebymultiplefilters
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `category`      | `Array of Strings. Each element is category ID` | Mention IDs of all the categories required.|
| `chapter`      | `Array of Numbers. Each element specifies number of chapters ` | Mention the required number of chapters.|

    Eg: 
    {
            "category" : ["61f80c81b0f077ecfbb76628",
                        "61f80ce4b0f077ecfbb7662a" ],
            "chapter" : [1,2,3]
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "courses" : <course schema>  ` | All courses under the given category will be displayed. |
|`500`|`"message" : "Internal Server Error"`| Server error |

#### METHOD 2 - Get courses of multiple categories with multiple chapter selection

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/searches/getcoursebymultiplefilters
```
 INPUT FORMAT

| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `category`      | `Array of Strings. Each element is category ID` | Mention IDs of all the categories required.|
| `chapter`      | `Array of Strings. Each element specifies range of chapters separated by / Eg. ["0/5", "5/10"] ` | Mention the required number of chapters.|

    Eg: 
    {
        "category" : ["61f80c81b0f077ecfbb76628",
                    "61f80ce4b0f077ecfbb7662a" ],
        "chapter" : ["0/5", "5/10"]
    }

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
| `200`      | ` "data" : <array of category objects>  ` | Category object will be as given below. |
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "_id": <String - ID of the category object>,
        "name": <String - Category Name>,
        "categoryImageUrl": <String - URL of the category icon>,
        "searchFrequency" : <Number - Number of times this category has been searched>
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
        "_id": <String - ID of the category object>,
        "name": <String - Category Name>,
        "categoryImageUrl": <String - URL of the category icon>,
        "searchFrequency" : <Number - Number of times this category has been searched>
    }

#### Get top searched courses under the particular category

```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/searches/topcourses
```
 INPUT FORMAT
| Body | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `category`      | `<String - ID of the category>` | Mention ID of the category|


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` "data" : <array of course objects>  ` | Courses in the category with Search Frequency in decending order. |
|`500`|`"message" : "Internal Server Error"`| Server error |


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
| `200`      | ` "data" : <object of course details>  ` | Course details in the format given below. |

    {
    "data": {
        "_id": <String - Course ID>,
        "name": <String - Name of the course>,
        "courseImageUrl" <String - URL of course image or thumbnail>,
        "category": {
             "_id": <String - Category ID>,
            "name": <String - Category name>,
            "categoryImageUrl": <String - URL of Category icon or image>,
            "searchFrequency" : <Number - Number of times this category has been searched>
        },
        "subcategories" : <Array - Each element is a subcategory>
        "courseContent": {
            "chapter": <Number - Number of chapters>,
            "lesson": <Number - Number of lessons>,
            "assignmentTest": <Number - Number of tests>,
            "totalLength": <String - Time required for entire course to complete (till now this data stored is in minutes)>
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
                                "passingGrade": <Number - Passing grade of the quiz>,
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
            "previewVideoUrl": <String - URL of the video>,
            "previewVideoThumbnail": <String - URL of the preview video thumbnail>,
            "previewVideoDuration": <String - Time duration of the preview video>,
            "longDescription": <String - Description of the course>,
            "courseIncludes": [
                {
                    "iconUrl": <String - Icons URL>,
                    "description": <String- Description>
                },
                {
                    "iconUrl": <String - Icons URL>,
                    "description": <String- Description>
                },
                {
                    "iconUrl": <String - Icons URL>,
                    "description": <String- Description>
                }
            ],
            "whatYouWillLearn": [
                {
                    "iconUrl": <String - Icons URL>,
                    "description": <String- Description>
                },
                {
                    "iconUrl": <String - Icons URL>,
                    "description": <String- Description>
                }
            ],
            "requirements": [
                <String - Array of requirements> 
            ],
            "instructorName": <String - Name of the instructor>,
            "instructorImageUrl": <String - Author's Image>,
            "instructorTitle": <String - Author's Title>,
            "instructorDescription": <String - Author's Description>,
            "__v": 0,
            "searchFrequency": <Number - number of this course was searched using name or ID)
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
| `200`      | `"coursesEnrolled" : Array of objects. Each object is a course` | All courses the user has enrolled in, res in the format given below. |
|`404` |  ` "message": "User has not enrolled in any course" ` | User has not enrolled in any of the courses. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "coursesEnrolled": [
            {
                "_id": <String - ID of this object>,
                "userId": <String - ID of the user>,
                "courseID": <String - ID of course>,
                "joinedOn": <String - Of type ISO>,
                "courseCompletionRate": <Number - Rate of course completion(in percentage but no symbol is appended(%))>
                "courseApprovalRate": <Number - Course approval rate>
                "completedOn": <String - Of type ISO>
                "__v": 0
            }, 
            {
                "_id": <String - ID of this object>,
                "userId": <String - ID of the user>,
                "courseID": <String - ID of course>,
                "joinedOn": <String - Of type ISO>,
                "courseCompletionRate": <Number - Rate of course completion(in percentage but no symbol is appended(%))>
                "courseApprovalRate": <Number - Course approval rate>
                "completedOn": <String - Of type ISO>
                "__v": 0
            }
        ]
    }


#### Get course progress(For use in any place where much detail of course progress is not required. E.g. to check if user has enrolled in the course etc)

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
| `200`      | `"progressData" : {} ` | Data of the form given below. |
| `409`      | ` "message" : " User has not enrolled in the course"` | User has not enrolled in course. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "progressData" : {

                "_id": <String - ID of this object>,
                "userId": <String - ID of the user>,
                "courseID": <String - ID of course>,
                "joinedOn": <String - Of type ISO>,
                "courseCompletionRate": <Number - Rate of course completion(in percentage but no symbol is appended(%))>
                "courseApprovalRate": <Number - Course approval rate>
                "completedOn": <String - Of type ISO>
                "__v": 0

        }
    }
            

#### My courses/Get all course progress (with course details that are required)

```http
  GET https://virtual-learn-api.herokuapp.com/api/v1/users/getmycourses
```
 INPUT FORMAT

| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|


OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | `"coursesEnrolled" : {} ` | Data of the form given below. |
| `409`      | ` "message" : " User has not enrolled in the course"` | User has not enrolled in course. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

        {
            "coursesEnrolled": [
                {
                    "progress": {
                        "_id": <String>,
                        "userId": <String>,
                        "courseID": <String>,
                        "joinedOn": <String>,
                        "courseCompletionRate": <Number>,
                        "courseApprovalRate": <Number>,
                        "completedOn": <String>
                    },
                    "course": {
                        "_id": <String>,
                        "name": <String>,
                        "courseImageUrl":<String>,
                        "courseContent": {
                            "chapter": <Number>,
                            "lesson": <Number>,
                            "assignmentTest": <Number>,
                            "totalLength": <Number>
                        }
                    }
                }
            ]
        }

### Update video progress
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/updatevideoprogress
```
| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
As the format below
    {
        "courseID": <String - ID of the course>,
        "chapterID" : <String - ID of the chapter>,
        "videoID" : <String - ID of the video>,
        "videoOrder" : <Number - Order of the video>,
        "progressRate" : <Number - (pass only numbers)percentage completed>,
        "watchedTill" : <String>
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` Data (can be ignored) ` | Status saved. |
| `403`      | ` message: "You are not enrolled in this course"` | User has not enrolled in course. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

### Update questionaire progress/result
```http
  POST https://virtual-learn-api.herokuapp.com/api/v1/users/updatequestionaireprogress
```
| Header | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `Authorization`      | `String` | Auth key received after user registration / login **Required**.|

| Body (form-data) | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
As the format below
    {
        "courseID": <String - ID of the course>,
        "chapterID" : <String - ID of the chapter>,
        "questionaireID" : <String - ID of the questionaire>,
        "approvalRate" : <Number - in percentage>,
        "right" : <Number - Number of right answers>,
        "wrong" : <Number - Number of wrong answers>
    }

OUTPUT FORMAT 
| Status Code | Response received     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `200`      | ` Data(can be ignored) ` | Status saved. |
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
| `200`      | ` <Object with array for videos, questionaire, chapter, course progress data> ` | Output as the format given below. |
|`401`| ` "message": "Authentication Failed" ` |Check auth key|
|`500`|`"message" : "Internal Server Error"`| Server error |

    {
        "videos": [
            {{
                "_id": <String>,
                "userId": <String>,
                "courseID": <String>,
                "chapterID": <String>,
                "videoID": <String>,
                "videoOrder": <Number>,
                "progressRate": <Number>,
                "watchedTill": <String>,
                "__v": 0
            },
            {
                "_id": <String>,
                "userId": <String>,
                "courseID": <String>,
                "chapterID": <String>,
                "videoID": <String>,
                "videoOrder": <Number>,
                "progressRate": <Number>,
                "watchedTill": <String>,
                "__v": 0
            },
            {
                "_id": <String>,
                "userId": <String>,
                "courseID": <String>,
                "chapterID": <String>,
                "videoID": <String>,
                "videoOrder": <Number>,
                "progressRate": <Number>,
                "watchedTill": <String>,
                "__v": 0
            }
        ],
        "questionaire": [
            {
                "_id": <String>,
                "userId": <String>,
                "courseID": <String>,
                "chapterID": <String>,
                "questionaireID": <String>,
                "approvalRate": <Number>,
                "right": <Number>,
                "wrong": <Number>,
                "__v": 0
            }
        ],
        "chapters": [
            {
                "_id": <String>,
                "userId": <String>,
                "courseID": <String>,
                "chapterID": <String>,
                "chapterOrder": <Number>,
                "chapterProgressRate": <Number>,
                "chapterApprovalRate": <Number>
                "__v": 0
            },
            {
                "_id": <String>,
                "userId": <String>,
                "courseID": <String>,
                "chapterID": <String>,
                "chapterOrder": <Number>,
                "chapterProgressRate": <Number>,
                "chapterApprovalRate": <Number>
                "__v": 0
            }
        ],
        "course": {
            "_id": <String>,
            "userId": <String>,
            "courseID": <String>,
            "joinedOn": <String>, 
            "courseCompletionRate": <Number>,
            "completedOn": <String>,
            "courseApprovalRate": <Number>,
            "__v": 0
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
            "timeDuration" : <String - Time Duration of the test>,
            "passingGrade" : <Number - Passing grade of the test>,
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
                    <String - ID of the second video>,

                ]
        
            },
            "questionnaire": {
                "questionID" : <String - ID of the questionaire>
                
            },
            "numberOfVideos": <Number - Number of videos>,
            "numberOfQuestions": <Number - Number of Questions in Questionaire>
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
        "previewVideoDuration" : <String - Time duration of the preview video>,
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


### Check Input
```http
    ANY_METHOD_HERE  https://virtual-learn-api.herokuapp.com/checkinput
```
